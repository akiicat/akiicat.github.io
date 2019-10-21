---
title: setup-google-cloud-for-third-application
tags:
  - Google Cloud Platform
  - Google Cloud IAM
  - GitHub
  - GitHub Actions
categories:
  - Google Cloud Platform
date: 2019-10-20 23:49:22
---


這次是將現有的 Google Cloud Functions (GCF) 專案透過 GitHub Actions 自動化部屬至 Google Cloud Platform (GCP)。GitHub Actions 是第三方的服務，GCP 須建立金鑰給第三方服務使用，這篇將會記錄要如何設定 GCP 的金鑰。

## 環境

- Google Cloud Platform: 需先建立專案，並開啟帳單功能，ProjectID 為 short-url-256304。

## 方法

### 建立憑證金鑰

1. 點選「 IAM 與管理員」
2. 點選「服務帳戶」
3. 點選「建立服務帳戶」

![GCP IAM STEP 1](/images/gcp-iam-step-1.png)

4. 輸入服務帳戶名稱
5. 點選「建立」

![GCP IAM STEP 2](/images/gcp-iam-step-2.png)

6. 點選「角色」-> 選擇要部署要應用程式「ＸＸＸ開發人員」（下圖以 Gloud Functions 為例） -> 點選「建立」

![GCP IAM STEP 3](/images/gcp-iam-step-3.png)

7. 點選「建立金鑰」

![GCP IAM STEP 4](/images/gcp-iam-step-4.png)

8. 選擇金鑰類型「JSON」
9. 點選「建立」，此時會下載一份金鑰檔，將這份檔案保存好
10. 點選「完成」

![GCP IAM STEP 5](/images/gcp-iam-step-5.png)

11. 建立完成畫面

![GCP IAM STEP 6](/images/gcp-iam-step-6.png)

### 賦予服務帳戶權限

這時候根據建立完還不能透過第三方部署應用程式，會出現以下錯誤訊息：

```shell
ERROR: (gcloud.functions.deploy) ResponseError: status=[403], code=[Forbidden], message=[Missing necessary permission iam.serviceAccounts.actAs for ***@appspot.gserviceaccount.com on project ***. 
 Please grant ***@appspot.gserviceaccount.com the roles/iam.serviceAccountUser role. 
 You can do that by running 'gcloud projects add-iam-policy-binding *** --member=***@appspot.gserviceaccount.com --role=roles/iam.serviceAccountUser' 
```

錯誤訊息有提示你要如何解決這個問題，所以照提示訊息輸入以下指令，需將 **PROJECT_ID** 與 **DEPLOY_NAME** 改成你現在使用的專案：

```shell
export PROJECT_ID=short-url-256304
export DEPLOY_NAME=deploy-to-gcp

gcloud iam service-accounts add-iam-policy-binding $PROJECT_ID@appspot.gserviceaccount.com  --member=serviceAccount:$DEPLOY_NAME@$PROJECT_ID.iam.gserviceaccount.com --role=roles/iam.serviceAccountUser --project=$PROJECT_ID
```

操作步驟：

1. 點選右上角圖示「啟用 Cloud Shell」
2. 在終端機上輸入賦予帳戶權限的指令

![GCP IAM STEP 7](/images/gcp-iam-step-7.png)

## 測試

使用的 Repo 是 [akiicat/short-url][1]，透過 GitHub Actions 自動部署至 Google Cloud Functions 的設定檔，注意 **jobs.deploy.steps[]** 裡的前兩個步驟：

```yaml
# .github/workflows/google-cloud-functions.yml
name: GoogleCloudFuncitons
on: [push]
jobs:
  deploy:
    name: GCP Authenticate
    runs-on: ubuntu-latest
    steps:
    - name: Setup Google Cloud
      uses: actions/gcloud/auth@master
      env:
        # https://github.com/actions/gcloud/issues/13#issuecomment-511596628
        GCLOUD_AUTH: ${{ secrets.GCLOUD_AUTH }}

    - name: GCP Setup Project ID
      uses: actions/gcloud/cli@master
      with:
        args: "config set project ${{ secrets.ProjectID }}"

    - name: GCP Functions List
      uses: actions/gcloud/cli@master
      with:
        args: "functions list"

    - name: Deploy to GCP
      uses: actions/gcloud/cli@master
      with:
        args: "functions deploy link --entry-point=Link --runtime=go111 --trigger-http"
```

第一個步驟（Setup Google Cloud），是使用先前下載 JSON的金鑰向 Google Cloud Platform 認證權限，這裡的 GCLOUD_AUTH 需先將 JSON 檔轉成 base64 的格式，可以參考[這篇 issue][5]：

```shell
$ base64 short-url-256304-287.json
ewogICJ0eXBlIjogInNlcnZpY2VfYWNjb3VudCIsCiAgInByb2plY3RfaWQiOiAic2hvcnQtdXJsLTI1NjMwNCIsCiAgI...
lcGxveS10by1nY2YlNDBzaG9ydC11cmwtMjU2MzA0LmlhbS5nc2VydmljZWFjY291bnQuY29tIgp9Cg==
```

第二個步驟（GCP Setup Project ID），是設定 Google Cloud Platform 的 Project ID

**secrets.GCLOUD_AUTH** 與 **secrets.ProjectID** 的設定請參考 [Virtual environments for GitHub Actions][4]

## Reference

- [Testing: Github akiicat/short-url Repo][1]
- [StackOverflow: The caller does not have permission][2]
- [Cloud Build documentation][3]
- [Virtual environments for GitHub Actions: Setting Secrets][4]
- [GitHub Actions: My secret is always invalid][5]

[1]: https://github.com/akiicat/short-url
[2]: https://stackoverflow.com/questions/51902661/error-gcloud-beta-functions-deploy-message-the-caller-does-not-have-perm
[3]: https://cloud.google.com/cloud-build/docs/configuring-builds/build-test-deploy-artifacts
[4]: https://help.github.com/en/articles/virtual-environments-for-github-actions#creating-and-using-secrets-encrypted-variables
[5]: https://github.com/actions/gcloud/issues/13#issuecomment-511596628


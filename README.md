# Run Splunk AppInspect requests from VS Code

[Visual Studio Code](https://code.visualstudio.com/), with the popular [REST Client](https://github.com/Huachao/vscode-restclient) extension, provides a workflow to test, develop and monitor REST endpoints.

The included file, `AppInspect.http` can perform the necessary steps to validate a custom built Splunk App, as documented on [dev.splunk.com](https://dev.splunk.com/enterprise/docs/releaseapps/appinspect/splunkappinspectapi/runappinspectrequestsapi).

Splunk provides documentation for validating an app via AppInspect API using cURL, Python and [Postman](https://www.getpostman.com/). The workflow developed here may be more suitable for those already working in VS Code and don't wish to install/buy additional applications.

## Requirements
* [VS Code](https://code.visualstudio.com/)
* VS Code [REST Client](https://github.com/Huachao/vscode-restclient) extension installed
* `splunk.com` credentials

*Recomended VS Code Extension*
* [Splunk Extension](https://github.com/splunk/vscode-extension1) is a strongly suggested extension for those developing for Splunk in VS Code.

## Configuration
Rename `example.env` to `.env` and replace the username and password with valid credentials for `splunk.com`.

Modify the first line in `AppInspect.http` to indicate the file that will be uploaded and validated.

## Steps
* Load the `AppInspect.http` file in VS Code
* Step 1 - Authenticate with the AppInspect endpoint and obtain an authentication token
   * Click the `Send Request` button associated with Step 1, and confirm in the `Response` pane that it returns a HTTP `200 OK` response code.
* Step 2 - Submit an app for validation
   * (*Optional*) If required, add tags to the included or excluded tags section of the form. Available tags are documented at the [AppInspect reference page](https://dev.splunk.com/enterprise/docs/releaseapps/appinspect/appinspectreferencetopics/appinspecttagreference/). Multiple tags can be included by duplicating this section. 
   * Common tags include 
      * `cloud` for Cloud Vetting
      * `self-service` for apps that can be installed in Splunk Cloud automatically
      * `py3_migration` to check the app is compatible with Python 3
      * `appapproval` for uploading an app to SplunkBase
      * `splunk_appinspect` for it to be certified on SplunkBase.
   * Click the `Send Request` button associated with Step 2, and confirm in the `Response` pane that it returns a HTTP `200 OK` response code.
* Step 3 - Check on the validation process
   * Click the `Send Request` button associated with Step 3. 
   * If the `status` field states `PENDING`, repeat Step 3 again after a minute or so.
* Step 4 - Download the validation report
   * If you require the validation report in a particular format, click the `Send Request` button associated with that HTML report.
   * Once completed, the HTML will appear in the Preview pane. Save this HTML by clicking on the `Save Response Body` button.
   * The saved HTML can be viewed in a browser, or submitted to Splunk Support to support validation.

## Example


    @filename = TA-Jira_Alert_Action.tar.gz

    ### Step 1. Authenticate with Splunk and get a token for subsequent requests
    # @name Authenticate
    GET https://api.splunk.com/2.0/rest/login/splunk HTTP/1.1
    Authorization: Basic {{$dotenv user}} {{$dotenv pass}}
    Cache-Control: no-cache

    ### 
    @token = {{Authenticate.response.body.$.data.token}}

    ### Step 2
    # @name Submit
    POST https://appinspect.splunk.com/v1/app/validate
    Authorization: Bearer {{token}}
    Cache-Control: no-Cache-Control
    max-messages: all
    Content-Type: multipart/form-data; boundary=----WebKitFormBoundary7MA4YWxkTrZu0gW

    ------WebKitFormBoundary7MA4YWxkTrZu0gW
    Content-Disposition: form-data; name="included_tags"

    appaproval
    ------WebKitFormBoundary7MA4YWxkTrZu0gW
    Content-Disposition: form-data; name="included_tags"

    splunk_appinspect
    ------WebKitFormBoundary7MA4YWxkTrZu0gW
    Content-Disposition: form-data; name="excluded_tags"

    py3_migration
    ------WebKitFormBoundary7MA4YWxkTrZu0gW
    Content-Disposition: form-data; name="app_package"; filename="{{filename}}"
    Content-Type: application/octet-stream

    < {{filename}}
    ------WebKitFormBoundary7MA4YWxkTrZu0gW--

    ###
    @request_id = {{Submit.response.body.$.request_id}}

    # Step 3. Status check
    GET https://appinspect.splunk.com/v1/app/validate/status/{{request_id}}
    Authorization: Bearer {{token}}
    Content-Type: application/json

    ###
    # Step 4b. Report - HTML
    GET https://appinspect.splunk.com/v1/app/report/{{request_id}}
    Authorization: Bearer {{token}}
    Content-Type: text/htmL
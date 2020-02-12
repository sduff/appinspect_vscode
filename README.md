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
   * (*Optional*) If required, add tags to the included or excluded tags section of the form. Available tags are documented at the [AppInspect reference page](https://dev.splunk.com/enterprise/docs/releaseapps/appinspect/appinspectreferencetopics/appinspecttagreference/). Multiple tags can be included by duplicating this section. Common tags include `cloud` for Cloud Vetting, `self-service` for apps that can be installed in Splunk Cloud automatically, and `py3_migration` to check the app is compatible with Python 3.
   * Click the `Send Request` button associated with Step 2, and confirm in the `Response` pane that it returns a HTTP `200 OK` response code.
* Step 3 - Check on the validation process
   * Click the `Send Request` button associated with Step 3. 
   * If the `status` field states `PENDING`, repeat Step 3 again after a minute or so.
* Step 4 - Download the validation report
   * If you require the validation report in a particular format, click the `Send Request` button associated with that report. The HTML report may be required for submissions to Splunk Support.

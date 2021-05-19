# slack_cloudview_alerts

# License
THIS SCRIPT IS PROVIDED TO YOU "AS IS."  TO THE EXTENT PERMITTED BY LAW, QUALYS HEREBY DISCLAIMS ALL WARRANTIES AND LIABILITY FOR THE PROVISION OR USE OF THIS SCRIPT.  IN NO EVENT SHALL THESE SCRIPTS BE DEEMED TO BE CLOUD SERVICES AS PROVIDED BY QUALYS

# Summary
Python script for pulling CloudView CSA Report from Qualys API and send report of
control failures to a Slack Channel with list of resource IDs per failed control. Accepts command line arguments to specify scope and option to also create a corresponding csv. Scope is required to run the script, csv argument is optional.

To run the script you will need:

1. Credentials for the Qualys user name and password - stored in the form of environment variables

The Script is configured to read environmental variables for user name and password
$QUALYS_API_USERNAME

> QUALYS_API_USERNAME stores the Qualys API User Name

> QUALYS_API_PASSWORD = Enter password when prompted.

To store password as environment variable

export $QUALYS_API_PASSWORD=APIpassword

Edit Line 74 and change to password=os.environ["QUALYS_API_PASSWORD"]


2. Qualys CloudView API endpoint URL for your Qualys Platform

3. Config file accountMap  - Requirements defined below

# Prerequisites
This script is written in Python 2.7.
The script relies on the following Python modules to execute: sys, requests, datetime, os, time, csv, getopt, logging, yaml, json, base64, logging.config

For module missing warnings/errors use PIP to install modules
> for Linux

`pip install pyyaml`

> for Windows

`python -m pip install pyyaml`



# Parameters:

  apiURL:

    Default: Qualys API URL for CloudView API endpoint. See https://www.qualys.com/docs/qualys-cloud-view-user-guide.pdf page 27    

  accountMap:

    File location of the Cloud Account map. This provides the information for the script to send CloudView CSA Reports to the specified Slack Channel for each Cloud account. Default value is specified as "./cloud-accounts.csv"

    *CSV File Requirements*
    *CSV columns* - cloud,name,accountId,BU,slackChannel,webHook
    The script uses the columns *cloud, accountId, BU, and webHook* by those column names. The other columns are not used in this version of the script. If the columns headers for *cloud, accountId, BU, and webHook are not included in the CSV*, the script will *error* and not execute.

    >cloud: specify the cloud for the account, acceptable values are: *aws, azure, or gcp*

    >accountId: AWS Account ID

    >BU: Business Unit for the specified account. Used if wanting to perform a perimeter scan of multiple accounts for a particular Business Unit.

    >webHook: specify the Slack incoming webhook for your Slack App.

# Slack Requirements
Incoming webhook to receive the CloudView CSA Report
For information on setting up an App Incoming Webhook check the Slack knowledge base site - https://get.slack.help/hc/en-us/articles/115005265063-Incoming-WebHooks-for-Slack

# Running slack_cloudview_alerts.py
This script is written in Python 2.7. It requires a command line argument(s) to run and can be executed using the following commands for syntax
    > python slack_cloudview_alerts.py -h

    > python slack_cloudview_alerts.py --help

*Required* Run report
"-r scope" "--report scope"
scope - accepts one of three input types

allAccount (case sensitive) - create report per account defined in cloud_accounts.csv

BU - create report per account for the specified Business Unit defined in cloud_accounts.csv

Account - create a report for a single account ID specified in cloud_accounts.csv

*Though listed as optional, one of the following command line arguments is require to run the script*
*Both options can be used to create a CloudView Report and send to a Slack Channel and to a CSV in the reports directory*

*Optional* Send CloudView Report to Slack webhook specified in cloud_accounts.csv
"-s" "--slack"

*Optional* Create CSV of report for each account in scope
"-c" "--csv"
Creates a CSV report for each account in scope in the ./reports directory. If you wish to change the location of the reports modify lines 90, 152, & 153
Line 90 sets the file path for the report
Line 152/153 checks for the existence of the reports folder and creates "./reports" if it does not exist.

# Logging
Logging configuration files is located in ./config/logging.yml. To change logging behavior, make changes in this file. For information on Python 2.7 logging visit https://docs.python.org/2/library/logging.html
Logging configuration
File Handler writes to ./log/cloudviewreports.log
Maximum Log size = 10 MB ( logging.yml line 18 - maxBytes: 10485760 # 10MB)
Backup file count = 5 (logging.yml line 19 - backupCount: 5)
Log Level = INFO (Change to WARNING or higher for production - logging.yml line 15 - level: INFO)

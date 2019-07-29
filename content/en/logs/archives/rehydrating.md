---
title: Rehydrating from Archives
kind: Documentation
description: "Capture log events from your archives back into Datadog."
---

<div class="alert alert-warning">
	Log Archive Rehydrating is in public beta. Request early access by contacting <a href="/help">Datadog Support</a>. Rehydrating is currently only supported for AWS S3 Archives, and for users of the Datadog US site. <a href="https://forms.gle/X4jhi13Rd2pFSuSHA">Send feedback for this feature</a>.
</div>

## Overview

Log Rehydration* enables you to capture log events from customer-owned storage-optimized archives back into Datadog's search-optimized [Log Explorer][1], so that you can use Datadog to analyze or investigate log events that are either old or were excluded from indexing.

## Historical views

With historical views, teams rehydrate archived log events precisely by timeframe and query filter to meet specific, unexpected use cases efficiently. To create a historical view, go the [Configuration page][2] of your Datadog account and select the [“Rehydrate From Archives” tab][3], then the “New Historical View” button.

{{< img src="logs/archives/log_archives_rehydrate_historical.png" alt="Historical Views" responsive="true" style="width:75%;">}}

### Add new historical views

1. **Select the archive** from which you wish to rehydrate log events. Only archives that are [configured to use role delegation][4] are available for rehydrating.

2. **Choose the time period** for which you wish to rehydrate log events. The time period must be older than 24 hours.

3. **Input the query**. The query syntax is the same as that of the [log explorer search][5], but is limited to log attributes, [reserved attributes][6], and free text search on the message. You must include a service argument.

4. **Name your historical view**. Names must begin with a lowercase letter and can only contain lowercase letters, numbers, and the `-` character.

5. (Optional) **Add a description** to give your team context about the purpose of the historical view.

{{< img src="logs/archives/log_archives_rehydrate_reload.png" alt="Reload from Archive" responsive="true" style="width:75%;">}}

A historical view can contain a maximum of 300 million log events. There is no limit to how large its time range can be, but if you expect a historical view may exceed that limit, make your query filter more specific.

### View historical view content

#### From the historical view page

After selecting “Rehydrate from Archive,” the historical view is marked as “pending” until its content is ready to be queried.

Once the content is rehydrated, the historical view is marked as active, and the link in the query column leads to the historical view in the log explorer.

#### From the log explorer

Alternatively, teams can find the historical view from the Log Explorer directly from the index selector. When selecting a historical view, a pop-up offers to set the timeframe to one that is relevant to the selected historical view.

{{< img src="logs/archives/log_archives_rehydrate_explorer.gif" alt="Log Explorer" responsive="true" style="width:75%;">}}

### Deleting historical views

Historical views stay in Datadog until you opt to delete them. You can mark a historical view to be deleted by selecting and confirming the delete icon at the far right of the historical view.

24 hours later, the historical view is definitively deleted; until that time, the team is able to cancel the deletion.

{{< img src="logs/archives/log_archives_rehydrate_delete.gif" alt="Deleting Historical Views" responsive="true" style="width:75%;">}}

## Setting up archive rehydrating

### Define a Datadog archive

An external archive must be configured in order to rehydrate data from it. [Follow the guide][7] to archive your logs in the available destinations.

### Permissions

Datadog requires the permission to read from your archives in order to rehydrate content from them. This permission can be changed at any time.

#### AWS S3 Bucket

In order to rehydrate log events from your archives, Datadog uses the IAM Role in your AWS account that you configured for [your AWS integration][8]. If you have not yet created that Role, [follow these steps to do so][9]. To allow that Role to rehydrate log events from your archives, add the following permission statement to its IAM policies. Be sure to edit the bucket names and, if desired, specify the paths that contain your log archives.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "DatadogUploadLogArchives",
            "Effect": "Allow",
            "Action": [
                "s3:PutObject"
            ],
            "Resource": [
                "arn:aws:s3:::<MY_BUCKET_NAME_1_/_MY_OPTIONAL_BUCKET_PATH_1>/*",
                "arn:aws:s3:::<MY_BUCKET_NAME_2_/_MY_OPTIONAL_BUCKET_PATH_2>/*"
            ]
        },
        {
            "Sid": "DatadogReloadLogArchivesListBucket",
            "Effect": "Allow",
            "Action": "s3:ListBucket",
            "Resource": [
                "arn:aws:s3:::<MY_BUCKET_NAME_1>",
                "arn:aws:s3:::<MY_BUCKET_NAME_2>"
            ]
        },
        {
            "Sid": "DatadogReloadLogArchivesGetObject",
            "Effect": "Allow",
            "Action": "s3:GetObject",
            "Resource": [
                "arn:aws:s3:::<MY_BUCKET_NAME_1_/_MY_OPTIONAL_BUCKET_PATH_1>/*",
                "arn:aws:s3:::<MY_BUCKET_NAME_2_/_MY_OPTIONAL_BUCKET_PATH_2>/*"
            ]
        }
    ]
}
```

#### Adding role delegation to S3 archives

Datadog only supports rehydrating from archives that have been configured to use role delegation to grant access. Once you have modified your Datadog IAM role to include the IAM policy above, ensure that each archive in your [archive configuration page][10] has the correct AWS Account + Role combination.

{{< img src="logs/archives/log_archives_rehydrate_configure_s3.png" alt="Adding role delegation to S3 archives" responsive="true" style="width:75%;">}}

### Feedback

Datadog would love to receive your feedback on this feature, especially as it is in early beta. You can submit your feedback directly [from this form][11].

*Log Rehydration is a trademark of Datadog, Inc.

[1]: /logs/explorer
[2]: https://app.datadoghq.com/logs/pipelines
[3]: https://app.datadoghq.com/logs/pipelines/historical-views
[4]: #permissions
[5]: /logs/explorer/search
[6]: /logs/?tab=ussite#reserved-attributes
[7]: /logs/archives/s3/?tab=roledelegation#create-and-configure-an-s3-bucket
[8]: https://app.datadoghq.com/account/settings#integrations/amazon-web-services
[9]: /integrations/amazon_web_services/?tab=allpermissions#installation
[10]: https://app.datadoghq.com/logs/pipelines/archives
[11]: https://forms.gle/X4jhi13Rd2pFSuSHA
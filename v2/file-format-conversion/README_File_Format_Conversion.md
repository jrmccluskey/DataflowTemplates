
Convert file formats between Avro, Parquet & CSV template
---
The File Format Conversion template is a batch pipeline that converts files
stored on Cloud Storage from one supported format to another.

The following format conversions are supported:
- CSV to Avro
- CSV to Parquet
- Avro to Parquet
- Parquet to Avro.


:memo: This is a Google-provided template! Please
check [Provided templates documentation](https://cloud.google.com/dataflow/docs/guides/templates/provided/file-format-conversion)
on how to use it without having to build from sources using [Create job from template](https://console.cloud.google.com/dataflow/createjob?template=File_Format_Conversion).

:bulb: This is a generated documentation based
on [Metadata Annotations](https://github.com/GoogleCloudPlatform/DataflowTemplates#metadata-annotations)
. Do not change this file directly.

## Parameters

### Required parameters

* **inputFileFormat**: File format of the input files. Needs to be either avro, parquet or csv.
* **outputFileFormat**: File format of the output files. Needs to be either avro or parquet.
* **inputFileSpec**: The Cloud Storage file pattern to search for CSV files. For example, `gs://mybucket/test-*.csv`.
* **outputBucket**: Cloud storage directory for writing output files. This value must end in a slash. For example, `gs://your-bucket/path/`.
* **schema**: Cloud storage path to the avro schema file. For example, `gs://your-bucket/your-path/schema.avsc`.

### Optional parameters

* **containsHeaders**: Input CSV files contain a header record (true/false). Only required if reading CSV files. Defaults to: false.
* **deadletterTable**: Messages failed to reach the target for all kind of reasons (e.g., mismatched schema, malformed json) are written to this table. For example, `your-project:your-dataset.your-table-name`.
* **delimiter**: The column delimiter of the input text files. Default: `,` For example, `,`.
* **csvFormat**: CSV format specification to use for parsing records. Default is: `Default`. See https://commons.apache.org/proper/commons-csv/apidocs/org/apache/commons/csv/CSVFormat.html for more details. Must match format names exactly found at: https://commons.apache.org/proper/commons-csv/apidocs/org/apache/commons/csv/CSVFormat.Predefined.html.
* **jsonSchemaPath**: The path to the JSON schema. Defaults to `null`. For example, `gs://path/to/schema`.
* **largeNumFiles**: Set to true if number of files is in the tens of thousands. Defaults to `false`.
* **csvFileEncoding**: The CSV file character encoding format. Allowed values are `US-ASCII`, `ISO-8859-1`, `UTF-8`, and `UTF-16`. Defaults to: UTF-8.
* **logDetailedCsvConversionErrors**: Set to `true` to enable detailed error logging when CSV parsing fails. Note that this may expose sensitive data in the logs (e.g., if the CSV file contains passwords). Default: `false`.
* **numShards**: The maximum number of output shards produced when writing. A higher number of shards means higher throughput for writing to Cloud Storage, but potentially higher data aggregation cost across shards when processing output Cloud Storage files. Default value is decided by Dataflow.
* **outputFilePrefix**: The prefix of the files to write to. Defaults to: output.



## Getting Started

### Requirements

* Java 17
* Maven
* [gcloud CLI](https://cloud.google.com/sdk/gcloud), and execution of the
  following commands:
  * `gcloud auth login`
  * `gcloud auth application-default login`

:star2: Those dependencies are pre-installed if you use Google Cloud Shell!

[![Open in Cloud Shell](http://gstatic.com/cloudssh/images/open-btn.svg)](https://console.cloud.google.com/cloudshell/editor?cloudshell_git_repo=https%3A%2F%2Fgithub.com%2FGoogleCloudPlatform%2FDataflowTemplates.git&cloudshell_open_in_editor=v2/file-format-conversion/src/main/java/com/google/cloud/teleport/v2/templates/FileFormatConversion.java)

### Templates Plugin

This README provides instructions using
the [Templates Plugin](https://github.com/GoogleCloudPlatform/DataflowTemplates#templates-plugin).

### Building Template

This template is a Flex Template, meaning that the pipeline code will be
containerized and the container will be executed on Dataflow. Please
check [Use Flex Templates](https://cloud.google.com/dataflow/docs/guides/templates/using-flex-templates)
and [Configure Flex Templates](https://cloud.google.com/dataflow/docs/guides/templates/configuring-flex-templates)
for more information.

#### Staging the Template

If the plan is to just stage the template (i.e., make it available to use) by
the `gcloud` command or Dataflow "Create job from template" UI,
the `-PtemplatesStage` profile should be used:

```shell
export PROJECT=<my-project>
export BUCKET_NAME=<bucket-name>

mvn clean package -PtemplatesStage  \
-DskipTests \
-DprojectId="$PROJECT" \
-DbucketName="$BUCKET_NAME" \
-DstagePrefix="templates" \
-DtemplateName="File_Format_Conversion" \
-f v2/file-format-conversion
```


The command should build and save the template to Google Cloud, and then print
the complete location on Cloud Storage:

```
Flex Template was staged! gs://<bucket-name>/templates/flex/File_Format_Conversion
```

The specific path should be copied as it will be used in the following steps.

#### Running the Template

**Using the staged template**:

You can use the path above run the template (or share with others for execution).

To start a job with the template at any time using `gcloud`, you are going to
need valid resources for the required parameters.

Provided that, the following command line can be used:

```shell
export PROJECT=<my-project>
export BUCKET_NAME=<bucket-name>
export REGION=us-central1
export TEMPLATE_SPEC_GCSPATH="gs://$BUCKET_NAME/templates/flex/File_Format_Conversion"

### Required
export INPUT_FILE_FORMAT=<inputFileFormat>
export OUTPUT_FILE_FORMAT=<outputFileFormat>
export INPUT_FILE_SPEC=<inputFileSpec>
export OUTPUT_BUCKET=<outputBucket>
export SCHEMA=<schema>

### Optional
export CONTAINS_HEADERS=false
export DEADLETTER_TABLE=<deadletterTable>
export DELIMITER=<delimiter>
export CSV_FORMAT=Default
export JSON_SCHEMA_PATH=<jsonSchemaPath>
export LARGE_NUM_FILES=false
export CSV_FILE_ENCODING=UTF-8
export LOG_DETAILED_CSV_CONVERSION_ERRORS=false
export NUM_SHARDS=0
export OUTPUT_FILE_PREFIX=output

gcloud dataflow flex-template run "file-format-conversion-job" \
  --project "$PROJECT" \
  --region "$REGION" \
  --template-file-gcs-location "$TEMPLATE_SPEC_GCSPATH" \
  --parameters "inputFileFormat=$INPUT_FILE_FORMAT" \
  --parameters "outputFileFormat=$OUTPUT_FILE_FORMAT" \
  --parameters "inputFileSpec=$INPUT_FILE_SPEC" \
  --parameters "containsHeaders=$CONTAINS_HEADERS" \
  --parameters "deadletterTable=$DEADLETTER_TABLE" \
  --parameters "delimiter=$DELIMITER" \
  --parameters "csvFormat=$CSV_FORMAT" \
  --parameters "jsonSchemaPath=$JSON_SCHEMA_PATH" \
  --parameters "largeNumFiles=$LARGE_NUM_FILES" \
  --parameters "csvFileEncoding=$CSV_FILE_ENCODING" \
  --parameters "logDetailedCsvConversionErrors=$LOG_DETAILED_CSV_CONVERSION_ERRORS" \
  --parameters "outputBucket=$OUTPUT_BUCKET" \
  --parameters "schema=$SCHEMA" \
  --parameters "numShards=$NUM_SHARDS" \
  --parameters "outputFilePrefix=$OUTPUT_FILE_PREFIX"
```

For more information about the command, please check:
https://cloud.google.com/sdk/gcloud/reference/dataflow/flex-template/run


**Using the plugin**:

Instead of just generating the template in the folder, it is possible to stage
and run the template in a single command. This may be useful for testing when
changing the templates.

```shell
export PROJECT=<my-project>
export BUCKET_NAME=<bucket-name>
export REGION=us-central1

### Required
export INPUT_FILE_FORMAT=<inputFileFormat>
export OUTPUT_FILE_FORMAT=<outputFileFormat>
export INPUT_FILE_SPEC=<inputFileSpec>
export OUTPUT_BUCKET=<outputBucket>
export SCHEMA=<schema>

### Optional
export CONTAINS_HEADERS=false
export DEADLETTER_TABLE=<deadletterTable>
export DELIMITER=<delimiter>
export CSV_FORMAT=Default
export JSON_SCHEMA_PATH=<jsonSchemaPath>
export LARGE_NUM_FILES=false
export CSV_FILE_ENCODING=UTF-8
export LOG_DETAILED_CSV_CONVERSION_ERRORS=false
export NUM_SHARDS=0
export OUTPUT_FILE_PREFIX=output

mvn clean package -PtemplatesRun \
-DskipTests \
-DprojectId="$PROJECT" \
-DbucketName="$BUCKET_NAME" \
-Dregion="$REGION" \
-DjobName="file-format-conversion-job" \
-DtemplateName="File_Format_Conversion" \
-Dparameters="inputFileFormat=$INPUT_FILE_FORMAT,outputFileFormat=$OUTPUT_FILE_FORMAT,inputFileSpec=$INPUT_FILE_SPEC,containsHeaders=$CONTAINS_HEADERS,deadletterTable=$DEADLETTER_TABLE,delimiter=$DELIMITER,csvFormat=$CSV_FORMAT,jsonSchemaPath=$JSON_SCHEMA_PATH,largeNumFiles=$LARGE_NUM_FILES,csvFileEncoding=$CSV_FILE_ENCODING,logDetailedCsvConversionErrors=$LOG_DETAILED_CSV_CONVERSION_ERRORS,outputBucket=$OUTPUT_BUCKET,schema=$SCHEMA,numShards=$NUM_SHARDS,outputFilePrefix=$OUTPUT_FILE_PREFIX" \
-f v2/file-format-conversion
```

## Terraform

Dataflow supports the utilization of Terraform to manage template jobs,
see [dataflow_flex_template_job](https://registry.terraform.io/providers/hashicorp/google/latest/docs/resources/dataflow_flex_template_job).

Terraform modules have been generated for most templates in this repository. This includes the relevant parameters
specific to the template. If available, they may be used instead of
[dataflow_flex_template_job](https://registry.terraform.io/providers/hashicorp/google/latest/docs/resources/dataflow_flex_template_job)
directly.

To use the autogenerated module, execute the standard
[terraform workflow](https://developer.hashicorp.com/terraform/intro/core-workflow):

```shell
cd v2/file-format-conversion/terraform/File_Format_Conversion
terraform init
terraform apply
```

To use
[dataflow_flex_template_job](https://registry.terraform.io/providers/hashicorp/google/latest/docs/resources/dataflow_flex_template_job)
directly:

```terraform
provider "google-beta" {
  project = var.project
}
variable "project" {
  default = "<my-project>"
}
variable "region" {
  default = "us-central1"
}

resource "google_dataflow_flex_template_job" "file_format_conversion" {

  provider          = google-beta
  container_spec_gcs_path = "gs://dataflow-templates-${var.region}/latest/flex/File_Format_Conversion"
  name              = "file-format-conversion"
  region            = var.region
  parameters        = {
    inputFileFormat = "<inputFileFormat>"
    outputFileFormat = "<outputFileFormat>"
    inputFileSpec = "<inputFileSpec>"
    outputBucket = "<outputBucket>"
    schema = "<schema>"
    # containsHeaders = "false"
    # deadletterTable = "<deadletterTable>"
    # delimiter = "<delimiter>"
    # csvFormat = "Default"
    # jsonSchemaPath = "<jsonSchemaPath>"
    # largeNumFiles = "false"
    # csvFileEncoding = "UTF-8"
    # logDetailedCsvConversionErrors = "false"
    # numShards = "0"
    # outputFilePrefix = "output"
  }
}
```

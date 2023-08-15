
# Data Processing and Upload Workflow

The deployment process for the Text Search feature involves several key stages, all of which are meticulously outlined in the <a href="https://github.com/gissemari/uploadDynamoS3">`UploadDynamo Repository`</a>. The purpose of this deployment is to upload videos to the AWS platform, later retrieving them in a Web Dictionary. The process comprises the following phases:

<p align="center">
  <img src="./assets/DynamoDB.png" alt="TextSearch" width="738">
</p>


## Database Structure Definition and Video Storage:
At the outset, the pipeline's architecture and structure are defined, determining whether a relational or non-relational database will be employed. For this project, the choice is a non-relational database—DynamoDB from AWS. Additionally, the AWS S3 service is utilized for video storage. This combination of DynamoDB and S3 provides a reliable and scalable infrastructure for managing video data. For more details refer to <a href="https://docs.aws.amazon.com/AmazonS3/latest/userguide/Welcome.html">`S3 Documentation`</a> and <a href="https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.html">`DynamoDB Documentation`</a>

## Lemma Extraction and Data Preparation:
The first step in the deployment process involves running the <a href="https://github.com/gissemari/uploadDynamoS3/blob/master/scriptGloss.py">`scriptGloss.py`</a> script for data preparation and lemma extraction.

The `scriptGloss.py` script plays a vital role in simplifying video filenames for the AWS platform and the web dictionary. It focuses on identifying the core lemmas within video filenames, which are the different ways a sign is annotated (Glosses). For instance, signs like "CAMINAR-AVE" and "CAMINAR-PERSONA" refer to different signs but share the same underlying meaning, which is the lemma "caminar." Similarly, variations like "ABURRIDO_1" and "ABURRIDO_2" should also be treated as the same lemma "aburrido." This process of finding lemmas from the filenames is essential for organizing and categorizing signs effectively.

The script employs the spaCy library to extract these lemmas. spaCy helps transform words in the filenames into their base forms, making it easier for the computer to process them consistently. During this process, the script also keeps track of the frequency of each lemma, forming a kind of vocabulary list that highlights which lemmas appear most often.

The result of running the script is a valuable file named `lemmaPUCP305.csv`. This file serves as a crucial reference point, connecting the original words in video filenames to their respective lemmas and indicating how frequently they appear. This file lays the foundation for the DynamoDB table that will help us efficiently manage and retrieve signs during subsequent steps. For detailed instructions on how to execute this script, you can refer to the <a href="https://github.com/gissemari/uploadDynamoS3">`UploadDynamo Repository`</a>.

## Adding TextSentences:
The process of adding sentences to the lemma data is a crucial step that enhances the completeness and usability of the database. This stage builds upon the `lemmaPUCP305.csv` file generated earlier and enriches it by associating each gloss with its corresponding sentence where it came from.

Using the script <a href="https://github.com/gissemari/uploadDynamoS3/blob/master/addingSentences.py">`adding_sentences.py`</a>, this task is automated to ensure consistency and accuracy. The script takes `lemmaPUCP305.csv` as input and extracts information from the ELAN annotation files. These annotation files provide translations and sentences associated with the glosses, contributing to a more comprehensive understanding of the context in which each sign is used.

The script performs a series of operations, such as parsing the file paths, locating ELAN annotation files, and extracting sentence annotations from them. These sentences are then linked to their respective glosses in the database. The result is a new file named `lemmaPUCP305-reviewed.csv`, which includes the original lemma data alongside the associated sample sentences.

This added layer of information enables the Dictionary LSP to show an example of use of the isolated sign in a sentence. For detailed instructions on how to execute this script, you can refer to the <a href="https://github.com/gissemari/uploadDynamoS3">`UploadDynamo Repository`</a>.

## Slowing Down Videos:
In the process of preparing the dataset for deployment, an important step is to optimize video content for smoother playback on the front-end. The script <a href="https://github.com/gissemari/uploadDynamoS3/blob/master/slowDownVideos.py">`slowDownVideos.py`</a> serves this purpose by modifying the playback speed of individual video files, thus enhancing the user experience by ensuring that short videos can be effectively viewed without any issues.

However, please note that this Python script does not directly slow down videos but instead generates a shell (sh) script that will be used to apply the video slowdown using the FFmpeg tool.

Here's how the process works:

1. **Python Script (`slowDownVideos.py`)**: This script is responsible for creating a shell script containing commands to modify the playback speed of the video files. The generated shell script will be named `slowDownVideos305.sh`.

2. **Generated Shell Script (`slowDownVideos305.sh`)**: The shell script contains a series of FFmpeg commands, each tailored to slow down a specific video file. By executing this shell script, you'll effectively apply the required changes to the videos.

It's important to note that the `slowDownVideos.py` script relies on information from a CSV file (`lemmaPUCP305-reviewed.csv` in this case) that includes details about the video files to be processed. Additionally, the video files themselves need to be present in the specified directory.

For detailed instructions on how to execute the `slowDownVideos.py` script, generate and execute the shell script, and fulfill any dependencies or requirements, please consult the documentation provided in the <a href="https://github.com/gissemari/uploadDynamoS3">`UploadDynamo Repository`</a>.

## Uploading to S3 Buckets:
The process of uploading videos to an AWS S3 bucket serves as a fundamental step in storing Isolated and Sentence Sign Language Videos. These videos are strategically stored in S3 to be seamlessly accessed by the Dictionary Web application whenever needed. This accessibility facilitates text search capabilities and the ability to display videos as part of the dictionary's functionalities. The URLs of these videos stored in the S3 buckets are integrated into the DynamoDB table's structure, enabling efficient retrieval and presentation within the website.

Here's a high-level overview of the upload process:

1. Python Script <a href="https://github.com/gissemari/uploadDynamoS3/blob/master/uploadS3boto3.py">`uploadS3boto3.py`</a>: This script serves as the main tool for uploading video files to the AWS S3 bucket. It utilizes the Boto3 library to establish a connection to AWS and interact with the designated S3 buckets.

2. CSV Input (`lemmaPUCP305-reviewed.csv`): The script relies on information from a CSV file that contains details about the video files to be uploaded. This CSV includes the video filenames, along with associated glosses or lemmas.

3. S3 Buckets (isolatedsigns and sentencesigns): The videos are uploaded to two distinct S3 buckets: isolatedsigns for isolated signs and sentencesigns for sentences.

   - isolatedsigns Bucket: This bucket is used to store individual or isolated sign videos. The script uploads each video with its associated gloss or lemma as the video's object name in the bucket.

   - sentencesigns Bucket: This bucket is used to store videos of sign sentences. Similar to the isolated signs, videos are uploaded to this bucket with their corresponding object names.

The `uploadS3boto3.py` script reads the CSV file, locates the video files, and then proceeds to upload each video to its designated S3 bucket. The script employs the Boto3 library's functionality to handle the uploading process securely and efficiently.

For a comprehensive guide on how to execute the `uploadS3boto3.py` script, upload videos to the designated S3 buckets, and ensure all necessary prerequisites are met, please refer to the documentation provided in the <a href="https://github.com/gissemari/uploadDynamoS3">`UploadDynamo Repository`</a>.

## Creating DynamoDB Table and Population:
To streamline this process, a Python script named <a href="https://github.com/gissemari/uploadDynamoS3/blob/master/createItemsDynamo.py">`createItemsDynamo.py`</a> is provided. This script utilizes the Boto3 library, which enables interactions with AWS services from within Python code. The purpose of this script is to create an efficient batch upload mechanism for adding video-related data to the DynamoDB table.

The script reads essential video details from a CSV file, such as `lemmaPUCP305-reviewed.csv`. These details include the sign's gloss, lemma, associated URLs for isolated and sentence videos stored in S3 buckets, corresponding text descriptions, and other metadata. Using the boto3 library, the script constructs and submits batch write requests to the DynamoDB table for efficient data insertion.

For comprehensive guidance on executing the <a href="https://github.com/gissemari/uploadDynamoS3/blob/master/createItemsDynamo.py">`createItemsDynamo.py`</a> script, uploading video-related data to the DynamoDB table, ensuring prerequisites are met, and integrating these details into your platform's functionality, please refer to the documentation available in <a href="https://github.com/gissemari/uploadDynamoS3">`uploadDynamo Repository`</a>.

<!-- 
# Data Processing and Upload Workflow

The deployment process for the Text Search feature involves several key stages, all of which are meticulously outlined in the <a href="https://github.com/gissemari/uploadDynamoS3">`UploadDynamo Repository`</a>. The purpose of this deployment is to upload videos to the AWS platform, later retrieving them in a Web Dictionary. The process comprises the following phases:

<p align="center">
  <img src="./assets/DynamoDB.png" alt="TextSearch" width="738">
</p>


## Database Structure Definition and Video Storage:
At the outset, the pipeline's architecture and structure are defined, determining whether a relational or non-relational database will be employed. For this project, the choice is a non-relational database—DynamoDB from AWS. Additionally, the AWS S3 service is utilized for video storage. This combination of DynamoDB and S3 provides a reliable and scalable infrastructure for managing video data. For more details refer to [`S3 Documentation`](https://docs.aws.amazon.com/AmazonS3/latest/userguide/Welcome.html) and [`Dynamo DB Documentation`](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.html)

## Lemma Extraction and Data Preparation:
The first step in the deployment process involves running the `scriptGloss.py` script for data preparation and lemma extraction. 

The `scriptGloss.py` script plays a vital role in simplifying video filenames for the AWS platform and the web dictionary. It focuses on identifying the core lemmas within video filenames, which are the different ways a sign is annotated (Glosses). For instance, signs like "CAMINAR-AVE" and "CAMINAR-PERSONA" refer to different signs but share the same underlying meaning, which is the lemma "caminar." Similarly, variations like "ABURRIDO_1" and "ABURRIDO_2" should also be treated as the same lemma "aburrido." This process of finding lemmas from the filenames is essential for organizing and categorizing signs effectively.

The script employs spaCy library to extract these lemmas. spaCy helps transform words in the filenames into their base forms, making it easier for the computer to process them consistently. During this process, the script also keeps track of the frequency of each lemma, forming a kind of vocabulary list that highlights which lemmas appear most often. 

The result of running the script is a valuable file named `lemmaPUCP305.csv`. This file serves as a crucial reference point, connecting the original words in video filenames to their respective lemmas and indicating how frequently they appear. This file lays the foundation for the DynamoDB table that will help us efficiently manage and retrieve signs during subsequent steps. For detailed instructions on how to execute this script, you can refer to the [`uploadDynamo Repository`](https://github.com/gissemari/uploadDynamoS3).

## Adding TextSentences:

The process of adding sentences to the lemma data is a crucial step that enhances the completeness and usability of the database. This stage builds upon the `lemmaPUCP305.csv` file generated earlier and enriches it by associating each gloss with its corresponding sentence where it came from.

Using the script `adding_sentences.py`, this task is automated to ensure consistency and accuracy. The script takes lemmaPUCP305.csv as input and extracts information from the ELAN annotation files. These annotation files provide translations and sentences associated with the glosses, contributing to a more comprehensive understanding of the context in which each sign is used.

The script performs a series of operations, such as parsing the file paths, locating ELAN annotation files, and extracting sentence annotations from them. These sentences are then linked to their respective glosses in the database. The result is a new file named `lemmaPUCP305-reviewed.csv`, which includes the original lemma data alongside the associated sample sentences.

This added layer of information enables the Dictionary LSP to show a example of use of the isolated sign in a sentence. For detailed instructions on how to execute this script, you can refer to the [`uploadDynamo Repository`](https://github.com/gissemari/uploadDynamoS3).

## Slowing Down Videos

In the process of preparing the dataset for deployment, an important step is to optimize video content for smoother playback on the front-end. The script `slowDownVideos.py` serves this purpose by modifying the playback speed of individual video files, thus enhancing the user experience by ensuring that short videos can be effectively viewed without any issues.

However, please note that this Python script does not directly slow down videos but instead generates a shell (sh) script that will be used to apply the video slowdown using the FFmpeg tool.

Here's how the process works:

1. **Python Script (slowDownVideos.py)**: This script is responsible for creating a shell script containing commands to modify the playback speed of the video files. The generated shell script will be named slowDownVideos305.sh.

2. **Generated Shell Script (slowDownVideos305.sh)**: The shell script contains a series of FFmpeg commands, each tailored to slow down a specific video file. By executing this shell script, you'll effectively apply the required changes to the videos.

It's important to note that the slowDownVideos.py script relies on information from a CSV file (lemmaPUCP305-reviewed.csv in this case) that includes details about the video files to be processed. Additionally, the video files themselves need to be present in the specified directory.

For detailed instructions on how to execute the slowDownVideos.py script, generate and execute the shell script, and fulfill any dependencies or requirements, please consult the documentation provided in the [`uploadDynamo Repository`](https://github.com/gissemari/uploadDynamoS3).


## Uploading to S3 Buckets:
The process of uploading videos to an AWS S3 bucket serves as a fundamental step in storing Isolated and Sentence Sign Language Videos. These videos are strategically stored in S3 to be seamlessly accessed by the Dictionary Web application whenever needed. This accessibility facilitates text search capabilities and the ability to display videos as part of the dictionary's functionalities. The URLs of these videos stored in the S3 buckets are integrated into the DynamoDB table's structure, enabling efficient retrieval and presentation within the website.

Here's a high-level overview of the upload process:

1. Python Script (uploadS3boto3.py): This script serves as the main tool for uploading video files to the AWS S3 bucket. It utilizes the Boto3 library to establish a connection to AWS and interact with the designated S3 buckets.

2. CSV Input (lemmaPUCP305-reviewed.csv): The script relies on information from a CSV file that contains details about the video files to be uploaded. This CSV includes the video filenames, along with associated glosses or lemmas.

3. S3 Buckets (isolatedsigns and sentencesigns): The videos are uploaded to two distinct S3 buckets: isolatedsigns for isolated signs and sentencesigns for sentences.

	- isolatedsigns Bucket: This bucket is used to store individual or isolated sign videos. The script uploads each video with its associated gloss or lemma as the video's object name in the bucket.

	- sentencesigns Bucket: This bucket is used to store videos of sign sentences. Similar to the isolated signs, videos are uploaded to this bucket with their corresponding object names.

The `uploadS3boto3.py` script reads the CSV file, locates the video files, and then proceeds to upload each video to its designated S3 bucket. The script employs the Boto3 library's functionality to handle the uploading process securely and efficiently.

For a comprehensive guide on how to execute the `uploadS3boto3.py` script, upload videos to the designated S3 buckets, and ensure all necessary prerequisites are met, please refer to the documentation provided in the [`uploadDynamo Repository`](https://github.com/gissemari/uploadDynamoS3).
## Creating DynamoDB Table and Population:
To streamline this process, a Python script named `createItemsDynamo.py` is provided. This script utilizes the Boto3 library, which enables interactions with AWS services from within Python code. The purpose of this script is to create an efficient batch upload mechanism for adding video-related data to the DynamoDB table.

The script reads essential video details from a CSV file, such as lemmaPUCP305-reviewed.csv. These details include the sign's gloss, lemma, associated URLs for isolated and sentence videos stored in S3 buckets, corresponding text descriptions, and other metadata. Using the boto3 library, the script constructs and submits batch write requests to the DynamoDB table for efficient data insertion.

For comprehensive guidance on executing the `createItemsDynamo.py` script, uploading video-related data to the DynamoDB table, ensuring prerequisites are met, and integrating these details into your platform's functionality, please refer to the documentation available in [`uploadDynamo Repository`](https://github.com/gissemari/uploadDynamoS3).  -->
<!-- # Deployment of Text Search

These scripts describe the pipeline or process to upload videos to the AWS platform to later retrieve them in a Web Dictionary. First define the structure of the database (relational or not relational). In our case, we are using a non relational DB, DynamoDB from AWS and S3 to store the videos. Then, we slow down individual videos of signs cut/segmented from sign sentences videos because front ends seems to have trouble with short videos (even less than a second). Here we explain how to create these scripts and run them to execute the upload.

<p align="center">
  <img src="./assets/DynamoDB.png" alt="TextSearch" width="738">
</p> -->



<!-- 
## Step 1: Run scriptGloss.py to Obtain Lemmas

<!-- To have only one example for each GLOSS, we group all the examples of each gloss and select randomly a video example. To work with gloss, text and lemmas, we use the SPACY library.

1. To run install SPACY, consider using a conda environment

	pip install -U pip setuptools wheel
	pip install -U spacy
	pip install spacy pandas spellchecker
	python -m spacy download es_dep_news_trf

2. Run the script using the command: `python scriptGloss.py --videoPath videoPath`, where videoPath is the path were segmented videos are located
3. The script extracts the lemma and other relevant information from the video filenames in the specified directory.
4. It exports an intermediate file `lemmaPUCP305.csv` with the original raw gloss, lemma, same lemma flag, path, and sentence video for each gloss.

## Step 2: Run addingSentences.py to add the TextSentences information to the csv file

1. Run the script using `python addingSentences.py`
2. The script will add the TextSentences column to a csv file `lemmaPUCP305-reviewed.csv` and may generate a `error_log.txt` if any gloss has errors at their folder name


## Step 3: Modify Speed of Videos

1. Create a list of files and paths in an CSV (or iterate over a folder structure with os.walk(path))
2. The command to modify videos is ffmpeg, visit the official FFmpeg website: [https://ffmpeg.org/](https://ffmpeg.org/), to download.
3. Run slowDownVideos.py, a script that exports in a sh file with a list of ffmpeg commands for each of the videos in the folders specified in a CSV (once more here instead of the list in the CSV file, we can iterate over a folder structure).
4. Run slowDownVideos.sh

	ffmpeg -i listo_1534 -filter:v "setpts=2.0*PTS" listo_output.mp4

where setpts is the parameter to control the speed, i.e., a value of 2 make it twice slower.

## Step 4: Set AWS Environment to Interact through Platform or Command Line

These steps can either be executed from the AWS platform or by command line through AWS API. We do a combination. To execute from the command line, each user needs an access and secret key. Create access key and secret key. Some info [here](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-appendix-sign-up.html)	

1. Installthe AWS CLI by referring to the [installation guide](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html).
2. Check if the AWS CLI is installed by running the following command:
   ```shell
   aws --version
   ```
3. Configure the AWS credentials to use the AWS CLI by following the [configuration guide](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html)


## Step 5: Integration with scripts for AWS: upload videos to S3

1. Log in to AWS console, go to S3 Console.
2. Create a bucket. In our case we store individual or isolated signs in the bucket 'isolatedsigns', and the sentences in 'sentencesigns'. If you can not see it, you can create another bucket.
3. Create/update a python file (uploadS3boto3.py) that uses the boto AWS library. The main function is s3_client.upload_file(file_name, bucket,object_name). 
4. Check or set the bucket variable to the name of your S3 buckets in the python script: `bucket = 'test-isolatedsigns'` and `bucketSentence = 'test-sentencesigns'`
5. Run the following command to execute the script: `python uploadS3boto.py`
6. The script will start uploading the videos to the specified S3 bucket. The progress and any errors encountered during the upload process will be displayed in the terminal.
7. Once the script completes, you can check your S3 bucket in the AWS console to verify that the videos have been successfully uploaded. -->
<!-- 4. Another option: the file to produce with they python script in the previous stage (step 3), should have this format:

	aws s3api put-object --bucket isolatedsigns --body C:\Users\Gissella_BejaranoNic\Documents\PeruvianSignLanguage\Data\testS3\[fileName] --key [lemma]

	Create this script for only 3-5 files, and test it by running the .sh (executable file), you might need to give execution permissions.

	Run: sh uploadS3.sh -->
<!-- 
## Step 6: Integration with scripts for AWS: Create databae DynamoDB

1. Create/update the createItemsDynamo.py to upload batches of maximum 25 items. The main function receives a json file with the different items, this is how DynamoDB create, updates or remove items.
2. Review the code and make sure the following variables are set correctly:
	- path: The path to the directory containing the video files or the CSV file with the video information.
	- dictTemplate: The template dictionary for creating DynamoDB items. Ensure that the keys match the attribute names in your DynamoDB table.
3. If needed, modify the code to extract the relevant information from your video files or CSV file. Update the dictActual dictionary accordingly to include the desired attributes and values (For example adding the Webname)

Note: If you need to update `lemmaPUCP305-reviewed.csv` adding a column make sure it is updated in the dictActual dictionary in the createItemsDynamo.py

4. Run the following command to execute the script: `python createItemsDynamo.py`
5. The script will read the video files or the CSV file, create DynamoDB items based on the provided template, and batch write the items to DynamoDB.
6. Once the script completes, you can verify the created items in your DynamoDB table using the AWS console. -->
<!-- 2. Another option: Create a Dynamo table: Dynamodb Standard or Dynamobdb Standard IA

	```
	aws dynamodb create-table \
		--table-name MusicCollection \
		--attribute-definitions AttributeName=Artist,AttributeType=S AttributeName=SongTitle,AttributeType=S \
		--key-schema AttributeName=Artist,KeyType=HASH AttributeName=SongTitle,KeyType=RANGE \
		--provisioned-throughput ReadCapacityUnits=1,WriteCapacityUnits=1
		
		
	aws dynamodb batch-write-item --request-items file://myfile.json

	https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_BatchWriteItem.html
	https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/dynamodb.html#DynamoDB.Client.batch_write_item
	https://turbofuture.com/computers/upload-files-aws-s3-and-dynamodb

	``` -->



<!-- 


Gabriel useful links

https://aws.amazon.com/amplify/
From Gabriel Paredes - AWS to Everyone 10:15 AM
https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/workbench.Visualizer.ImportCSV.html
https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/workbench.settingup.html
https://aws.amazon.com/es/blogs/aws-spanish/choosing-the-right-dynamodb-partition-key/



Not useful

Create an access point to be able to run the script. It asks for policy/statement

s3://arn:aws:s3:us-east-1:769818850490:accesspoint/accessdiccionario
arn:aws:s3:us-east-1:769818850490:accesspoint/accessdiccionario
accessdiccionario-bttpqeon8hz9jhrtmkut3etz3zysquse1a-s3alias


DOES NOT WORK

	{
	"Sid": "Statement1",
	"Principal": {},
	"Effect": "Allow",
	"Action": [
		"s3:ListJobs",
		"s3:ListMultiRegionAccessPoints"
	],
	"Resource": []
}

SH DOES NOT WORK - I HAD TO INSTALL AWS CLI

bash: logInfo: command not found
curl: Can't open 'C:\Users\Gissella_BejaranoNic\Documents\PeruvianSignLanguage\Data\testS3*'!
curl: try 'curl --help' or 'curl --manual' for more information
curl: (26) Failed to open/read local data from file/application
bash: logInfo: command not found -->


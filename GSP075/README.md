# 1 - Extract, Analyze, and Translate Text from Images with the Cloud ML APIs

## Dados
username: student-01-a7e6a48d69a8@qwiklabs.net
password: wANUTHkwYIA6
project ID: qwiklabs-gcp-04-48c2e2ff0dbd 

### Verificar a conta 
```shell script
gcloud auth list
```

<!-- 
ACTIVE: *
ACCOUNT: student-01-a7e6a48d69a8@qwiklabs.net

To set the active account, run:
    $ gcloud config set account `ACCOUNT` 
-->

### Verificar a conta 
```shell script
gcloud config list project
```

<!-- 
[core]
project = qwiklabs-gcp-04-48c2e2ff0dbd 
-->

# Task 1. Create an API key
<!-- 
Since you'll be using curl to send a request to the Vision API, generate an API key to pass in your request URL.

To create an API key, navigate to: Navigation Menu > APIs & services > Credentials.

Click + Create Credentials.

In the drop down menu, select API key.

Next, copy the key you just generated and click Close.

Now save the API key to an environment variable to avoid having to insert the value of your API key in each request.

Run the following in Cloud Shell, replacing <your_api_key> with the key you just copied:

export API_KEY=<YOUR_API_KEY>

Click Check my progress to verify your performed task.
-->

### Run the following in Cloud Shell, replacing <your_api_key> with the key you just copied:
```shell script
export API_KEY=AIzaSyCOxoFRtgfJMqBdIZmBQ7bHx_lwoUQPaUI
echo $API_KEY
```

# Task 2. Upload an image to a Cloud Storage bucket
<!-- 
Create a Cloud Storage bucket
There are two ways to send an image to the Vision API for image detection: by sending the API a base64 encoded image string, or passing it the URL of a file stored in Cloud Storage. For this lab you'll create a Cloud Storage bucket to store your images.

Navigate to the Navigation menu > Cloud Storage browser in the Console, then click Create bucket.

Give your bucket a unique name:<filled in at lab start>-bucket.

After naming your bucket, click Choose how to control access to objects.

Uncheck the box for Enforce public access prevention on this bucket.

Choose Fine-grained under Access Control and click Create.

Upload an image to your bucket
Right click on the following image of a French sign, then click Save image as and save it to your computer as sign.jpg.
Le Bien Public French sign

Navigate to the bucket you just created in the Cloud Storage browser and click Upload files, then select sign.jpg.
Next you'll allow the file to be viewed publicly while keeping the access to the bucket private.

Click on the 3 dots for the image file:
Objects tab with uploaded sign image file, and more options button highlighted

Select Edit access.

Click Add Entry and set the following:

Select Public for the Entity.
Ensure allUsers is the value for the Name.
Select Reader for the Access.
Entity, Name and Access fields of newly added entry highlighted

Click Save.
You'll now see that the file has public access.

Now that you have the file in your bucket, you're ready to create a Vision API request, passing it the URL of this picture.

Click Check my progress to verify your performed task. 
-->

# Task 3. Create your Cloud Vision API request
<!-- 
In your Cloud Shell environment, create an ocr-request.json file, then add the code below to the file, replacing my-bucket-name with the name of the bucket you created. You can create the file using one of your preferred command line editors (nano, vim, emacs) or click the pencil icon to open the code editor in Cloud Shell:
Open Editor button displaying the pencil icon

Add the following to your ocr-request.json file:
{
  "requests": [
      {
        "image": {
          "source": {
              "gcsImageUri": "gs://my-bucket-name/sign.jpg"
          }
        },
        "features": [
          {
            "type": "TEXT_DETECTION",
            "maxResults": 10
          }
        ]
      }
  ]
}

You're going to use the TEXT_DETECTION feature of the Cloud Vision API. This will run optical character recognition (OCR) on the image to extract text. 
-->

### Create File
```shell script
touch ocr-request.json
nano ocr-request.json
```

### File Contains
```shell script
{
  "requests": [
      {
        "image": {
          "source": {
              "gcsImageUri": "gs://qwiklabs-gcp-04-48c2e2ff0dbd/sign.jpg"
          }
        },
        "features": [
          {
            "type": "TEXT_DETECTION",
            "maxResults": 10
          }
        ]
      }
  ]
}
```


# Task 4. Call the text detection method
<!-- 
In Cloud Shell, call the Cloud Vision API with curl:
curl -s -X POST -H "Content-Type: application/json" --data-binary @ocr-request.json  https://vision.googleapis.com/v1/images:annotate?key=${API_KEY}

The first part of your response should look like the following:

{
  "responses": [
    {
      "textAnnotations": [
        {
          "locale": "fr",
          "description": "LE BIEN PUBLIC\nles dépêches\nPour Obama,\nla moutarde\nest\nde Dijon\n",
          "boundingPoly": {
            "vertices": [
              {
                "x": 138,
                "y": 40
              },
              {
                "x": 622,
                "y": 40
              },
              {
                "x": 622,
                "y": 795
              },
              {
                "x": 138,
                "y": 795
              }
            ]
          }
        },
        {
          "description": "LE",
          "boundingPoly": {
            "vertices": [
              {
                "x": 138,
                "y": 99
              },
              {
                "x": 274,
                "y": 82
              },
              {
                "x": 283,
                "y": 157
              },
              {
                "x": 147,
                "y": 173
              }
            ]
          }
        },
        {
          "description": "BIEN",
          "boundingPoly": {
            "vertices": [
              {
                "x": 291,
                "y": 79
              },
              {
                "x": 413,
                "y": 64
              },
              {
                "x": 422,
                "y": 139
              },
              {
                "x": 300,
                "y": 154
              }
            ]
          }
            ...
      ]
}]
}
The OCR method is able to extract lots of text from the image.

The first piece of data you get back from textAnnotations is the entire block of text the API found in the image. This includes:

the language code (in this case fr for French)
a string of the text
a bounding box indicating where the text was found in the image
Then you get an object for each word found in the text with a bounding box for that specific word.

Note: For images with more text, the Cloud Vision API also has a DOCUMENT_TEXT_DETECTION feature. This response includes additional information and breaks text down into page, blocks, paragraphs, and words.
Unless you speak French you probably don't know what this says. The next step is translation.

Run the following curl command to save the response to an ocr-response.json file so it can be referenced later:
curl -s -X POST -H "Content-Type: application/json" --data-binary @ocr-request.json  https://vision.googleapis.com/v1/images:annotate?key=${API_KEY} -o ocr-response.json
-->

### Call API
```shell script
curl -s -X POST -H "Content-Type: application/json" --data-binary @ocr-request.json  https://vision.googleapis.com/v1/images:annotate?key=${API_KEY}
```

### Call API
```shell script
curl -s -X POST -H "Content-Type: application/json" --data-binary @ocr-request.json  https://vision.googleapis.com/v1/images:annotate?key=${API_KEY} -o ocr-response.json
```

# Task 5. Send text from the image to the Translation API
<!-- 
The Translation API can translate text into 100+ languages. It can also detect the language of the input text. To translate the French text into English, pass the text and the language code for the target language (en-US) to the Translation API.

First, create a translation-request.json file and add the following to it:
{
  "q": "your_text_here",
  "target": "en"
}
Copied!
q is where you'll pass the string to translate.

Save the file.

Run this Bash command in Cloud Shell to extract the image text from the previous step and copy it into a new translation-request.json (all in one command):

STR=$(jq .responses[0].textAnnotations[0].description ocr-response.json) && STR="${STR//\"}" && sed -i "s|your_text_here|$STR|g" translation-request.json
Copied!
Now you're ready to call the Translation API. This command will also copy the response into a translation-response.json file:
curl -s -X POST -H "Content-Type: application/json" --data-binary @translation-request.json https://translation.googleapis.com/language/translate/v2?key=${API_KEY} -o translation-response.json
Copied!
Run this command to inspect the file with the Translation API response:
cat translation-response.json
Copied!
Now you can understand more of what the sign said!

{
  "data": {
    "translations": [
      {
        "translatedText": "TO THE PUBLIC GOOD the dispatches For Obama, the mustard is from Dijon",
        "detectedSourceLanguage": "fr"
      }
    ]
  }
}
In the response:

translatedText contains the resulting translation
detectedSourceLanguage is fr, the ISO language code for French.
The Translation API supports 100+ languages, all of which are listed in the Language support reference.

In addition to translating the text from our image, you might want to do more analysis on it. That's where the Natural Language API comes in handy. Onward to the next step!
-->


### Create File
```shell script
touch translation-request.json
nano translation-request.json
```

### File Contains
```shell script
{
  "q": "your_text_here",
  "target": "en"
}
```

### Run Script
```shell script
STR=$(jq .responses[0].textAnnotations[0].description ocr-response.json) && STR="${STR//\"}" && sed -i "s|your_text_here|$STR|g" translation-request.json
```

### Call API
```shell script
curl -s -X POST -H "Content-Type: application/json" --data-binary @translation-request.json https://translation.googleapis.com/language/translate/v2?key=${API_KEY} -o translation-response.json
```

### Show Response
```shell script
cat translation-response.json
```


# Task 6. Analyzing the image's text with the Natural Language API
<!-- 
The Natural Language API helps you understand text by extracting entities, analyzing sentiment and syntax, and classifying text into categories. Use the analyzeEntities method to see what entities the Natural Language API can find in the text from your image.

To set up the API request, create a nl-request.json file with the following:
{
  "document":{
    "type":"PLAIN_TEXT",
    "content":"your_text_here"
  },
  "encodingType":"UTF8"
}
Copied!
In the request, you're telling the Natural Language API about the text you're sending:

type: supported type values are PLAIN_TEXT or HTML.

content: pass the text to send to the Natural Language API for analysis. The Natural Language API also supports sending files stored in Cloud Storage for text processing. To send a file from Cloud Storage, replace content with gcsContentUri and use the value of the text file's uri in Cloud Storage.

encodingType: tells the API which type of text encoding to use when processing the text. The API will use this to calculate where specific entities appear in the text.

Run this Bash command in Cloud Shell to copy the translated text into the content block of the Natural Language API request:
STR=$(jq .data.translations[0].translatedText  translation-response.json) && STR="${STR//\"}" && sed -i "s|your_text_here|$STR|g" nl-request.json
Copied!
The nl-request.json file now contains the translated English text from the original image. Time to analyze it!

Call the analyzeEntities endpoint of the Natural Language API with this curl request:
curl "https://language.googleapis.com/v1/documents:analyzeEntities?key=${API_KEY}" \
  -s -X POST -H "Content-Type: application/json" --data-binary @nl-request.json
Copied!
If you scroll through the response you can see the entities the Natural Language API found:

{
  "entities": [
    {
      "name": "dispatches",
      "type": "OTHER",
      "metadata": {},
      "salience": 0.3560996,
      "mentions": [
        {
          "text": {
            "content": "dispatches",
            "beginOffset": 23
          },
          "type": "COMMON"
        }
      ]
    },
    {
      "name": "mustard",
      "type": "OTHER",
      "metadata": {},
      "salience": 0.2878307,
      "mentions": [
        {
          "text": {
            "content": "mustard",
            "beginOffset": 38
          },
          "type": "COMMON"
        }
      ]
    },
    {
      "name": "Obama",
      "type": "PERSON",
      "metadata": {
        "mid": "/m/02mjmr",
        "wikipedia_url": "https://en.wikipedia.org/wiki/Barack_Obama"
      },
      "salience": 0.16260329,
      "mentions": [
        {
          "text": {
            "content": "Obama",
            "beginOffset": 31
          },
          "type": "PROPER"
        }
      ]
    },
    {
      "name": "Dijon",
      "type": "LOCATION",
      "metadata": {
        "mid": "/m/0pbhz",
        "wikipedia_url": "https://en.wikipedia.org/wiki/Dijon"
      },
      "salience": 0.08129317,
      "mentions": [
        {
          "text": {
            "content": "Dijon",
            "beginOffset": 54
          },
          "type": "PROPER"
        }
      ]
    }
  ],
  "language": "en"
}
For entities that have a wikipedia page, the API provides metadata including the URL of that page along with the entity's mid. The mid is an ID that maps to this entity in Google's Knowledge Graph. To get more information on it, you could call the Knowledge Graph API, passing it this ID. For all entities, the Natural Language API tells us the places it appeared in the text (mentions), the type of entity, and salience (a [0,1] range indicating how important the entity is to the text as a whole). In addition to English, the Natural Language API also supports the languages listed in the Language Support reference.

Looking at this image it's relatively easy to pick out the important entities, but if you had a library of thousands of images this would be much more difficult. OCR, translation, and natural language processing can help to extract meaning from large datasets of images.

Click Check my progress to verify your performed task. 
-->

### Create File
```shell script
touch nl-request.json
nano nl-request.json
```

### File Contains
```shell script
{
  "document":{
    "type":"PLAIN_TEXT",
    "content":"your_text_here"
  },
  "encodingType":"UTF8"
}
```

### Run Script
```shell script
STR=$(jq .data.translations[0].translatedText  translation-response.json) && STR="${STR//\"}" && sed -i "s|your_text_here|$STR|g" nl-request.json
```

### Call API
```shell script
curl "https://language.googleapis.com/v1/documents:analyzeEntities?key=${API_KEY}" -s -X POST -H "Content-Type: application/json" --data-binary @nl-request.json
```

### Call API
```shell script
curl "https://language.googleapis.com/v1/documents:analyzeEntities?key=${API_KEY}" -s -X POST -H "Content-Type: application/json" --data-binary @nl-request.json -o nl-response.json
```

### Show Response
```shell script
cat nl-response.json
```


# Congratulations!
<!-- 
You've learned how to combine 3 different machine learning APIs: the Vision API's OCR method extracted text from an image, then the Translation API translated that text to English and the Natural Language API to found entities in that text. You can use these APIs together to extract meaning from large datasets of images.

Next steps / learn more
Learn more from the tutorials and docs for Vision, Translation, and Natural Language
Google Cloud training and certification
...helps you make the most of Google Cloud technologies. Our classes include technical skills and best practices to help you get up to speed quickly and continue your learning journey. We offer fundamental to advanced level training, with on-demand, live, and virtual options to suit your busy schedule. Certifications help you validate and prove your skill and expertise in Google Cloud technologies.

Manual Last Updated February 14, 2024

Lab Last Tested October 06, 2023

Copyright 2024 Google LLC All rights reserved. Google and the Google logo are trademarks of Google LLC. All other company and product names may be trademarks of the respective companies with which they are associated.
-->
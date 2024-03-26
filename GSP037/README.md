# 2 - Detect Labels, Faces, and Landmarks in Images with the Cloud Vision API

## Dados
username: student-01-a7e6a48d69a8@qwiklabs.net
password: wANUTHkwYIA6
project ID: qwiklabs-gcp-04-48c2e2ff0dbd

### Verificar a conta 
```shell script
gcloud auth list
```

<!-- ACTIVE: *
ACCOUNT: student-01-a7e6a48d69a8@qwiklabs.net

To set the active account, run:
    $ gcloud config set account `ACCOUNT` -->

### Verificar a conta 
```shell script
gcloud config list project
```

<!-- [core]
project = qwiklabs-gcp-04-48c2e2ff0dbd -->

# Task 1. Create an API key
<!-- 
Since you'll be using curl to send a request to the Vision API, you'll need to generate an API key to pass in your request URL.

To create an API key, from the Navigation menu go to APIs & Services > Credentials in the Cloud Console.

Click Create Credentials and select API key.

Create Credentials page displaying API key option

Next, copy the key you just generated and click Close.
Click Check my progress below to check your lab progress. 
Next, save it to an environment variable to avoid having to insert the value of your API key in each request.

In Cloud Shell run the following command to set your project ID as an environment variable:-->

### Run the following in Cloud Shell, replacing <your_api_key> with the key you just copied:
```shell script
export API_KEY=AIzaSyAoYpaYp7PQyi4CD4K3mKeJ32e-o36Pfc0
echo $API_KEY
```

# Task 2. Upload an image to a Cloud Storage bucket
<!-- 
There are two ways to send an image to the Cloud Vision API for image detection: by sending the API a base64 encoded image string, or passing it the URL of a file stored in Cloud Storage.

You'll be using a Cloud Storage URL. The first step is to create a Cloud Storage bucket to store your images.

From the Navigation menu, select Cloud Storage > Buckets. Next to Buckets, click Create.

Give your bucket a unique name:qwiklabs-gcp-04-48c2e2ff0dbd-bucket.

After naming your bucket, click Choose how to control access to objects.

Uncheck Enforce public access prevention on this bucket and select the Fine-grained circle.

All other settings for your bucket can remain as the default setting.

Click Create. -->

<!-- Upload an image to your bucket
Right click on the following image of donuts, then click Save image as and save it to your computer as donuts.png.
Donuts

Go to the bucket you just created and click UPLOAD FILES, then select donuts.png.
Bucket details page with the UPLOAD FILES button highlighted

You should see the file in your bucket.

Now you need to make this image publicly available.

Click on the 3 dots for your image and select Edit access.

Click Add entry then enter the following:

Entity: Public
Name: allUsers
Access: Reader
Then click Save.

With the file in your bucket, you're ready to create a Cloud Vision API request, passing it the URL of this donuts picture.

Click Check my progress below to check your lab progress. -->

# Task 3. Create your request
<!-- 
Create a request.json file in Cloud Shell.

Using the Cloud Shell code editor (by clicking the pencil icon in the Cloud Shell ribbon),
Open Editor button

or your preferred command line editor (nano, vim, or emacs), create a request.json file.

Type or paste the following code into the file:
Note: Replace my-bucket-name with the name of your storage bucket.
{
  "requests": [
      {
        "image": {
          "source": {
              "gcsImageUri": "gs://my-bucket-name/donuts.png"
          }
        },
        "features": [
          {
            "type": "LABEL_DETECTION",
            "maxResults": 10
          }
        ]
      }
  ]
}

Save the file. -->

### Create File
```shell script
touch request.json
nano request.json
```

### File Contains
```shell script
{
  "requests": [
      {
        "image": {
          "source": {
              "gcsImageUri": "gs://qwiklabs-gcp-04-48c2e2ff0dbd-bucket/donuts.png"
          }
        },
        "features": [
          {
            "type": "LABEL_DETECTION",
            "maxResults": 10
          }
        ]
      }
  ]
}
```

# Task 4. Label detection
<!-- 
The first Cloud Vision API feature you'll use is label detection. This method will return a list of labels (words) of what's in your image.

Call the Cloud Vision API with curl:
curl -s -X POST -H "Content-Type: application/json" --data-binary @request.json  https://vision.googleapis.com/v1/images:annotate?key=${API_KEY}

Your response should look something like the following:


The API was able to identify the specific type of donuts these are, powdered sugar. Cool! For each label the Vision API found, it returns a:

description with the name of the item.
score, a number from 0 - 1 indicating how confident it is that the description matches what's in the image.
mid value that maps to the item's mid in Google's Knowledge Graph. You can use the mid when calling the Knowledge Graph API to get more information on the item. -->

### Call API
```shell script
curl -s -X POST -H "Content-Type: application/json" --data-binary @request.json  https://vision.googleapis.com/v1/images:annotate?key=${API_KEY}
```

### Call API
```shell script
curl -s -X POST -H "Content-Type: application/json" --data-binary @request.json  https://vision.googleapis.com/v1/images:annotate?key=${API_KEY} -o response.json
```

# Task 5. Web detection
<!-- 
In addition to getting labels on what's in your image, the Cloud Vision API can also search the internet for additional details on your image. Through the API's WebDetection method, you get a lot of interesting data back:

A list of entities found in your image, based on content from pages with similar images.
URLs of exact and partial matching images found across the web, along with the URLs of those pages.
URLs of similar images, like doing a reverse image search.
To try out web detection, use the same image of beignets and change one line in the request.json file (you can also venture out into the unknown and use an entirely different image).

Edit the request.json file - under the features list, change type from LABEL_DETECTION to WEB_DETECTION. The request.json should now look like this:
{
  "requests": [
      {
        "image": {
          "source": {
              "gcsImageUri": "gs://my-bucket-name/donuts.png"
          }
        },
        "features": [
          {
            "type": "WEB_DETECTION",
            "maxResults": 10
          }
        ]
      }
  ]
}

Save the file.

To send it to the Cloud Vision API, use the same curl command as before (just press the up arrow in Cloud Shell):

curl -s -X POST -H "Content-Type: application/json" --data-binary @request.json  https://vision.googleapis.com/v1/images:annotate?key=${API_KEY}

Dive into the response, starting with webEntities. Here are some of the entities this image returned:
{
  "responses": [
    {
      "webDetection": {
        "webEntities": [
          {
            "entityId": "/m/0z5n",
            "score": 0.8868,
            "description": "Application programming interface"
          },
          {
            "entityId": "/m/07kg1sq",
            "score": 0.3139,
            "description": "Encapsulation"
          },
          {
            "entityId": "/m/0105pbj4",
            "score": 0.2713,
            "description": "Google Cloud Platform"
          },
          {
            "entityId": "/m/01hyh_",
            "score": 0.2594,
            "description": "Machine learning"
          },
          ...
        ]
This image has been used in many presentations on Cloud ML APIs, which is why the API found the entities "Machine learning" and "Google Cloud Platform".

If you inpsect the URLs under fullMatchingImages, partialMatchingImages, and pagesWithMatchingImages, you'll notice that many of the URLs point to this lab site (super meta!).

Say you wanted to find other images of beignets, but not the exact same images. That's where the visuallySimilarImages part of the API response comes in handy. Here are a few of the visually similar images it found:

        "visuallySimilarImages": [
          {
            "url": "https://media.istockphoto.com/photos/cafe-du-monde-picture-id1063530570?k=6&m=1063530570&s=612x612&w=0&h=b74EYAjlfxMw8G-G_6BW-6ltP9Y2UFQ3TjZopN-pigI="
          },
          {
            "url": "https://s3-media2.fl.yelpcdn.com/bphoto/oid0KchdCqlSqZzpznCEoA/o.jpg"
          },
          {
            "url": "https://s3-media1.fl.yelpcdn.com/bphoto/mgAhrlLFvXe0IkT5UMOUlw/348s.jpg"
          },

          ...

] 
You can navigate to those URLs to see the similar images:

powdered sugar beignet image 1

powdered sugar beignet image 2

powdered sugar beignet image 3

And now you probably really want a powdered sugar beignet (sorry)! This is similar to searching by an image on Google Images.

With Cloud Vision you can access this functionality with an easy to use REST API and integrate it into your applications. -->

### File Contains
```shell script
{
  "requests": [
      {
        "image": {
          "source": {
              "gcsImageUri": "gs://qwiklabs-gcp-04-48c2e2ff0dbd-bucket/donuts.png"
          }
        },
        "features": [
          {
            "type": "WEB_DETECTION",
            "maxResults": 10
          }
        ]
      }
  ]
}
```

### Call API
```shell script
curl -s -X POST -H "Content-Type: application/json" --data-binary @request.json  https://vision.googleapis.com/v1/images:annotate?key=${API_KEY}
```

### Call API
```shell script
curl -s -X POST -H "Content-Type: application/json" --data-binary @request.json  https://vision.googleapis.com/v1/images:annotate?key=${API_KEY} -o response_web_detection.json
```

# Task 6. Face detection
<!-- Next explore the face detection methods of the Vision API.

The face detection method returns data on faces found in an image, including the emotions of the faces and their location in the image.

Upload a new image
To use this method, you'll upload a new image with faces to the Cloud Storage bucket.

Right click on the following image, then click Save image as and save it to your computer as selfie.png.
Selfie image displaying two other people taking selfie images of themselves

Now upload it to your Cloud Storage bucket the same way you did before, and make it public.
Click Check my progress below to check your lab progress. -->

<!-- Updating request file
Next, update your request.json file with the following, which includes the URL of the new image, and uses face and landmark detection instead of label detection. Be sure to replace my-bucket-name with the name of your Cloud Storage bucket:
{
  "requests": [
      {
        "image": {
          "source": {
              "gcsImageUri": "gs://my-bucket-name/selfie.png"
          }
        },
        "features": [
          {
            "type": "FACE_DETECTION"
          },
          {
            "type": "LANDMARK_DETECTION"
          }
        ]
      }
  ]
}

Save the file.
Calling the Vision API and parsing the response
Now you're ready to call the Vision API using the same curl command you used above:
curl -s -X POST -H "Content-Type: application/json" --data-binary @request.json  https://vision.googleapis.com/v1/images:annotate?key=${API_KEY}

Take a look at the faceAnnotations object in the response. You'll notice the API returns an object for each face found in the image - in this case, three. Here's a clipped version of the response:
{
      "faceAnnotations": [
        {
          "boundingPoly": {
            "vertices": [
              {
                "x": 669,
                "y": 324
              },
              ...
            ]
          },
          "fdBoundingPoly": {
            ...
          },
          "landmarks": [
            {
              "type": "LEFT_EYE",
              "position": {
                "x": 692.05646,
                "y": 372.95868,
                "z": -0.00025268539
              }
            },
            ...
          ],
          "rollAngle": 0.21619819,
          "panAngle": -23.027969,
          "tiltAngle": -1.5531756,
          "detectionConfidence": 0.72354823,
          "landmarkingConfidence": 0.20047489,
          "joyLikelihood": "LIKELY",
          "sorrowLikelihood": "VERY_UNLIKELY",
          "angerLikelihood": "VERY_UNLIKELY",
          "surpriseLikelihood": "VERY_UNLIKELY",
          "underExposedLikelihood": "VERY_UNLIKELY",
          "blurredLikelihood": "VERY_UNLIKELY",
          "headwearLikelihood": "VERY_LIKELY"
        }
        ...
     }
}
boundingPoly gives you the x,y coordinates around the face in the image.
fdBoundingPoly is a smaller box than boundingPoly, focusing on the skin part of the face.
landmarks is an array of objects for each facial feature, some you may not have even known about. This tells us the type of landmark, along with the 3D position of that feature (x,y,z coordinates) where the z coordinate is the depth. The remaining values give you more details on the face, including the likelihood of joy, sorrow, anger, and surprise.
The response you're reading is for the person standing furthest back in the image - you can see he's making a kind of a silly face which explains the joyLikelihood of LIKELY.
-->

### Create File
```shell script
rm request.json
touch request.json
nano request.json
```

### File Contains
```shell script
{
  "requests": [
      {
        "image": {
          "source": {
              "gcsImageUri": "gs://gs://qwiklabs-gcp-04-48c2e2ff0dbd-bucket/selfie.png"
          }
        },
        "features": [
          {
            "type": "FACE_DETECTION"
          },
          {
            "type": "LANDMARK_DETECTION"
          }
        ]
      }
  ]
}
```

### Call API
```shell script
curl -s -X POST -H "Content-Type: application/json" --data-binary @request.json  https://vision.googleapis.com/v1/images:annotate?key=${API_KEY}
```

### Call API
```shell script
curl -s -X POST -H "Content-Type: application/json" --data-binary @request.json  https://vision.googleapis.com/v1/images:annotate?key=${API_KEY} -o response_face_detection.json
```

<!-- Task 7. Landmark annotation
Landmark detection can identify common (and obscure) landmarks. It returns the name of the landmark, its latitude and longitude coordinates, and the location of where the landmark was identified in an image.

Upload a new image
To use this method, you'll upload a new image to the Cloud Storage bucket.

Right click on the following image, then click Save image as and save it to your computer as city.png.
Image of city

Citation: Saint Basil's Cathedral, Moscow, Russia (December 19, 2019) by Adrien Wodey on Unsplash, the free media repository. Retrieved from https://unsplash.com/photos/multicolored-dome-temple-yjyWCNx0J1U. This file is licensed under the Unsplash license.

Now upload it to your Cloud Storage bucket the same way you did before, and make it public.
Click Check my progress below to check your lab progress. -->

<!-- Updating request file
Next, update your request.json file with the following, which includes the URL of the new image, and uses landmark detection. Be sure to replace my-bucket-name with the name of your Cloud Storage bucket:
{
  "requests": [
      {
        "image": {
          "source": {
              "gcsImageUri": "gs://my-bucket-name/city.png"
          }
        },
        "features": [
          {
            "type": "LANDMARK_DETECTION",
            "maxResults": 10
          }
        ]
      }
  ]
}

Calling the Vision API and parsing the response
Now you're ready to call the Vision API using the same curl command you used above:
curl -s -X POST -H "Content-Type: application/json" --data-binary @request.json  https://vision.googleapis.com/v1/images:annotate?key=${API_KEY}

Look at the landmarkAnnotations part of the response:
      "landmarkAnnotations": [
        {
          "mid": "/m/0hm_7",
          "description": "Red Square",
          "score": 0.8557956,
          "boundingPoly": {
            "vertices": [
              {},
              {
                "x": 503
              },
              {
                "x": 503,
                "y": 650
              },
              {
                "y": 650
              }
            ]
          },
          "locations": [
            {
              "latLng": {
                "latitude": 55.753930299999993,
                "longitude": 37.620794999999994
              }
...
The Cloud Vision API was able to identify where the picture was taken and provides the map coordinates of the location (Saint Basil's Cathedral in Red Square, Moscow, Russia).

The values in this response should look similar to the labelAnnotations response above:

the mid of the landmark
it's name (description)
a confidence score
The boundingPoly shows the region in the image where the landmark was identified.
The locations key tells us the latitude longitude coordinates of the picture. -->

### Create File
```shell script
rm request.json
touch request.json
nano request.json
```

### File Contains
```shell script
{
  "requests": [
      {
        "image": {
          "source": {
              "gcsImageUri": "gs://qwiklabs-gcp-04-48c2e2ff0dbd-bucket/city.png"
          }
        },
        "features": [
          {
            "type": "LANDMARK_DETECTION",
            "maxResults": 10
          }
        ]
      }
  ]
}
```

### Call API
```shell script
curl -s -X POST -H "Content-Type: application/json" --data-binary @request.json  https://vision.googleapis.com/v1/images:annotate?key=${API_KEY}
```

### Call API
```shell script
curl -s -X POST -H "Content-Type: application/json" --data-binary @request.json  https://vision.googleapis.com/v1/images:annotate?key=${API_KEY} -o response_landmark_detection.json
```


# Task 8. Object localization
<!-- 
The Vision API can detect and extract multiple objects in an image with Object Localization. Object localization identifies multiple objects in an image and provides a LocalizedObjectAnnotation for each object in the image. Each LocalizedObjectAnnotation identifies information about the object, the position of the object, and rectangular bounds for the region of the image that contains the object.

Object localization identifies both significant and less-prominent objects in an image.

Object information is returned in English only. Cloud Translation can translate English labels into various other languages.

To use this method, you'll use an existing image on the internet and update the request.json file.

Updating request file
Update your request.json file with the following, which includes the URL of the new image, and uses object localization.
{
  "requests": [
    {
      "image": {
        "source": {
          "imageUri": "https://cloud.google.com/vision/docs/images/bicycle_example.png"
        }
      },
      "features": [
        {
          "maxResults": 10,
          "type": "OBJECT_LOCALIZATION"
        }
      ]
    }
  ]
}

Calling the Vision API and parsing the response
Now you're ready to call the Vision API using the same curl command you used above:
curl -s -X POST -H "Content-Type: application/json" --data-binary @request.json  https://vision.googleapis.com/v1/images:annotate?key=${API_KEY}

Next, look at the localizedObjectAnnotations part of the response:
{
  "responses": [
    {
      "localizedObjectAnnotations": [
        {
          "mid": "/m/01bqk0",
          "name": "Bicycle wheel",
          "score": 0.89648587,
          "boundingPoly": {
            "normalizedVertices": [
              {
                "x": 0.32076266,
                "y": 0.78941387
              },
              {
                "x": 0.43812272,
                "y": 0.78941387
              },
              {
                "x": 0.43812272,
                "y": 0.97331065
              },
              {
                "x": 0.32076266,
                "y": 0.97331065
              }
            ]
          }
        },
        {
          "mid": "/m/0199g",
          "name": "Bicycle",
          "score": 0.886761,
          "boundingPoly": {
            "normalizedVertices": [
              {
                "x": 0.312,
                "y": 0.6616471
              },
              {
                "x": 0.638353,
                "y": 0.6616471
              },
              {
                "x": 0.638353,
                "y": 0.9705882
              },
              {
                "x": 0.312,
                "y": 0.9705882
              }
            ]
          }
        },
...
As you can see, the Vision API was able to tell that this picture contains a bicycle and a bicycle wheel. The values in this response should look similar to the labelAnnotations response above: the mid of the object, it's name (name), a confidence score, and the boundingPoly shows the region in the image where the object was identified.

Furthermore, the boundingPoly has a normalizedVertices key, which gives you the coordinates of the object in the image. These coordinates are normalized to a range of 0 to 1, where 0 represents the top left of the image, and 1 represents the bottom right of the image.

Great! You successfully used the Vision API to analyze an image and extract information about the objects in the image.
 -->

### Create File
```shell script
rm request.json
touch request.json
nano request.json
```

### File Contains
```shell script
{
  "requests": [
    {
      "image": {
        "source": {
          "imageUri": "https://cloud.google.com/vision/docs/images/bicycle_example.png"
        }
      },
      "features": [
        {
          "maxResults": 10,
          "type": "OBJECT_LOCALIZATION"
        }
      ]
    }
  ]
}
```

### Call API
```shell script
curl -s -X POST -H "Content-Type: application/json" --data-binary @request.json  https://vision.googleapis.com/v1/images:annotate?key=${API_KEY}
```

### Call API
```shell script
curl -s -X POST -H "Content-Type: application/json" --data-binary @request.json  https://vision.googleapis.com/v1/images:annotate?key=${API_KEY} -o response_object_localization.json
```


# Task 9. Explore other Vision API methods
<!-- You've looked at the Vision API's label, face, landmark detection and object localization methods, but there are three others you haven't explored. Dive into the Method: images.annotate documentation to learn about the other three:

Logo detection: Identify common logos and their location in an image.
Safe search detection: Determine whether or not an image contains explicit content. This is useful for any application with user-generated content. You can filter images based on four factors: adult, medical, violent, and spoof content.
Text detection: Run OCR to extract text from images. This method can even identify the language of text present in an image.
 -->

# Congratulations!
<!-- 
You've learned how to analyze images with the Vision API. In this lab you passed the API the Cloud Storage URL of different images, and it returned the labels, faces, landmarks, and objects it found in the image. You can also pass the API a base64 encoded string of an image, which is useful if you want to analyze an image that's stored in a database or in memory.

Next steps / learn more
Check out the Cloud Vision API tutorials in the documentation
Find a Cloud Vision API sample in your favorite language on GitHub
Check out the Entity and Sentiment Analysis with the Natural Language API lab.
Google Cloud training and certification
...helps you make the most of Google Cloud technologies. Our classes include technical skills and best practices to help you get up to speed quickly and continue your learning journey. We offer fundamental to advanced level training, with on-demand, live, and virtual options to suit your busy schedule. Certifications help you validate and prove your skill and expertise in Google Cloud technologies.

Manual Last Updated February 22, 2024

Lab Last Tested February 22, 2024

Copyright 2024 Google LLC All rights reserved. Google and the Google logo are trademarks of Google LLC. All other company and product names may be trademarks of the respective companies with which they are associated. 
-->
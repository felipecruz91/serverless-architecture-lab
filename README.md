# Serverless Architecture Lab from the Azure Bootcamp at Lyngby, Denmark.
Extract license plate numbers using Azure Computer Vision API.

![Architecture diagram](https://github.com/felipecruz91/serverless-architecture-lab/blob/master/docs/images/ArchitectureDiagram.PNG)

## Description

The solution begins with vehicle photos being uploaded to an Azure Storage blobs container, as they are captured. A blob storage trigger fires on each image upload, executing the photo processing Azure Function endpoint (on the left-hand side of the diagram), which in turn sends the photo to the Cognitive Services Computer Vision API OCR service to extract the license plate data. If processing was successful and the license plate number was returned, the function submits a new Event Grid event, along with the data, to an Event Grid topic with an event type called “savePlateData”. However, if the processing was unsuccessful, the function submits an Event Grid event to the topic with an event type called  “queuePlateForManualCheckup”. Two separate functions are configured to trigger when new events are added to the Event Grid topic, each filtering on a specific event type, both saving the relevant data to the appropriate Azure Cosmos DB collection for the outcome, using the Cosmos DB output binding. A Logic App that runs on a 15-minute interval executes an Azure Function via its HTTP trigger, which is responsible for obtaining new license plate data from Cosmos DB and exporting it to a new CSV file saved to Blob storage. If no new license plate records are found to export, the Logic App sends an email notification to the Customer Service department via their Office 365 subscription. Application Insights is used to monitor all of the Azure Functions in real-time as data is being processed through the serverless architecture. This real-time monitoring allows you to observe dynamic scaling first-hand and configure alerts when certain events take place.

©2018 Microsoft Corporation

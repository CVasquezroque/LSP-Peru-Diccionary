# Backend Implementation

In order to establish backend connections between the web application and the Text Search functionalities, a connection between AWS DynamoDB services and the web was essential. This was achieved by utilizing AWS Lambda, allowing the creation of a defined trigger (in this case, a text search). When this trigger is invoked, the Lambda function is called, which then directs the request to DynamoDB. Notably, two distinct text search situations had to be considered during implementation. The first is a direct text search, and the second is a "text search by sign," where the AI's prediction result from the sign search is used to search DynamoDB for the corresponding example video.

## AWS Lambda Implementation

For this purpose, a single Lambda function was designed to accommodate both text search situations. To achieve this, DynamoDB had to be modified to include an indexName that references the second situation. Two primary scripts, 'build-word-query.js' and 'handler.js,' are utilized within the Lambda function.

<p align="center">
  <img src="./assets/Lambda_query.jpg" alt="lambda folder" width="780">
</p>


AWS Lambda Name: sign-language-dev-queryWord

### handler.js:

```
"use strict";
const AWS = require('aws-sdk');
const { success } = require('./shared/responses');
const { buildWordQuery , buildIAResultsQuery} = require('./queries/build-word-query');

const getItemsByWord = (word, indexType) => {

  const docClient = new AWS.DynamoDB.DocumentClient();
  
  let wordQuery;
  
  if (indexType === 'wordSearch') {
    wordQuery = buildWordQuery(word);
  }
  else if (indexType === 'AiResultSearch'){
    wordQuery = buildIAResultsQuery(word);
  }
  // or else{}
  
  return word ? docClient.query(wordQuery).promise() : docClient.scan(wordQuery).promise();
}

module.exports.queryWord = async (event) => {
  const { word, indexType }  = event.queryStringParameters; 
  const { Items } = await getItemsByWord(word, indexType)
  return success(Items)
};


```

The 'handler.js' module plays a central role in managing incoming requests to the AWS Lambda function. It interacts with DynamoDB to retrieve data based on the provided parameters.

The core functionality resides within the 'getItemsByWord' function. This function takes 'word' and 'indexType' as parameters. Depending on the 'indexType,' it constructs a query using the appropriate query-building function from the 'build-word-query.js' module. The generated query is then executed against the DynamoDB table using the AWS.DynamoDB.DocumentClient() instance. The choice between a query or a scan operation is based on the presence of the 'word' parameter.

The 'queryWord' async function, exported from this module, serves as the entry point for Lambda invocations. It extracts the 'word' and 'indexType' parameters from the incoming event's queryStringParameters and calls 'getItemsByWord' to retrieve relevant data from DynamoDB. The successful result is encapsulated in an HTTP response using the 'success' function from the 'shared/responses' module.

### build-word-query.js:

```
const TableName = "gloss";

module.exports.buildWordQuery = (word) => word?{
  TableName,
  KeyConditionExpression: '#sign_gloss = :sign_gloss',
  ExpressionAttributeNames: {
    '#sign_gloss': 'sign_gloss',
  },
  ExpressionAttributeValues: {
    ':sign_gloss': word,
  },
} : {
  TableName
};

module.exports.buildIAResultsQuery = (word) => word?{
  TableName,
  IndexName: 'sign_gloss_var-index',
  KeyConditionExpression: '#sign_gloss_var = :sign_gloss_var',
  ExpressionAttributeNames: {
    '#sign_gloss_var': 'sign_gloss_var',
  },
  ExpressionAttributeValues: {
    ':sign_gloss_var': word,
  },
} : {
  TableName
};


```

The 'build-word-query.js' module encompasses the query-building functions employed by the 'handler.js' module. These functions construct DynamoDB queries tailored for specific search scenarios. The module exports two primary functions: 'buildWordQuery' and 'buildIAResultsQuery.'

The 'buildWordQuery' function is designed to craft a query that searches for a 'gloss' within the DynamoDB table. It assembles a query with pertinent parameters, including TableName, KeyConditionExpression, ExpressionAttributeNames, and ExpressionAttributeValues.

The 'buildIAResultsQuery' function is specifically intended for constructing queries on the 'sign_gloss_var-index' index within the 'gloss' table. This function configures the query with parameters optimized for efficient indexed data searches.

Both query-building functions accept a 'word' parameter, using it to form a query that matches the provided word value.

## React Implementation

The implementation of the "Text Search" feature is organized within the dedicated <a href="https://github.com/JoeNatan30/SpanishToPSLTranslationService/tree/main/website/dev_stratlabs/src/features/text-search" target="_blank">`text-search`</a> directory. This directory houses all the essential components, views, routes, and services required for text-based searches. It is structured into three primary sections: *components*, *views*, and *support files*. Within the components folder, you will find reusable UI elements, including the specific component responsible for AI-based text search functionality. The views folder contains the <a href="https://github.com/JoeNatan30/SpanishToPSLTranslationService/blob/main/website/dev_stratlabs/src/features/text-search/views/TextSearch.jsx" target="_blank">`TextSearch.jsx`</a> file, where users engage with the <a href="https://dvlsp.link/search-by-text" target="_blank">search feature</a>. The routes associated with this feature are defined in <a href="https://github.com/JoeNatan30/SpanishToPSLTranslationService/blob/main/website/dev_stratlabs/src/features/text-search/text-search.routes.js" target="_blank">`text-search.routes.js`</a>, ensuring smooth navigation. The feature's logic is powered by <a href="https://github.com/JoeNatan30/SpanishToPSLTranslationService/blob/main/website/dev_stratlabs/src/features/text-search/text-search.services.js" target="_blank">`text-search.services.js`</a>, managing the intricate process of conducting text searches. This organized structure promotes code reusability, facilitates the separation of concerns, and streamlines collaborative development efforts.

The <a href="https://github.com/JoeNatan30/SpanishToPSLTranslationService/blob/main/website/dev_stratlabs/src/features/text-search/views/TextSearch.jsx" target="_blank">`TextSearch.jsx`</a> component is at the core of the text search implementation. This component handles the interaction with users who input keywords for conducting searches. It effectively utilizes states to manage the search status, messages, and results. It is also equipped to support an autocomplete system, which is currently disabled. Furthermore, it adeptly handles all aspects of search-related actions, encompassing error management and notifications.

The <a href="https://github.com/JoeNatan30/SpanishToPSLTranslationService/blob/1151ccc7ff2f4d7635c156e719739b67ad18616e/website/dev_stratlabs/src/features/text-search/views/TextSearch.jsx#L62C10-L62C20" target="_blank">`TextSearch`</a> component encapsulates the logic responsible for executing searches when users input a query and activate the "Search" button. The `onSearch` function responds to this event, coordinating the entire search process. This function collaborates with the <a href="https://github.com/JoeNatan30/SpanishToPSLTranslationService/blob/main/website/dev_stratlabs/src/features/text-search/text-search.services.js" target="_blank">`textSearchService`</a> to execute the search operation, update results and messages based on the outcome, and manage any potential errors.

In addition to the underlying logic, the component's JSX code handles rendering the search modal and presenting the results in the form of cards (Card components).


### Integration with `textSearchService`

The `TextSearch` feature seamlessly integrates with the <a href="https://github.com/JoeNatan30/SpanishToPSLTranslationService/blob/main/website/dev_stratlabs/src/features/sign-search/sign-search.services.js" target="_blank">`textSearchService`</a> service, which is referenced from <a href="https://github.com/JoeNatan30/SpanishToPSLTranslationService/blob/main/website/dev_stratlabs/src/features/text-search/text-search.services.js" target="_blank">`text-search.services.js`</a>.

Within <a href="https://github.com/JoeNatan30/SpanishToPSLTranslationService/blob/main/website/dev_stratlabs/src/features/text-search/text-search.services.js" target="_blank">`text-search.services.js`</a>, the service encapsulates the intricate logic required for performing text-based searches using an external API, leveraging the Axios library. This service accepts a search query and an index type as input, initiates a request to the API, processes the resulting data into a customized format, and subsequently returns the transformed results.

The API request is directed to the URL defined in <a href="https://github.com/JoeNatan30/SpanishToPSLTranslationService/blob/1151ccc7ff2f4d7635c156e719739b67ad18616e/website/dev_stratlabs/src/utils/axios-config.js#L5" target="_blank">`API_URLS.WORDS`</a>, which is specified in the <a href="https://github.com/JoeNatan30/SpanishToPSLTranslationService/blob/1151ccc7ff2f4d7635c156e719739b67ad18616e/website/dev_stratlabs/src/utils/axios-config.js" target="_blank">`axios-config`</a> file. By configuring `baseURL: API_URLS.WORDS` within Axios, all requests made using the Axios client (API) will have `WORDS` as their base URL.

The constant <a href="https://github.com/JoeNatan30/SpanishToPSLTranslationService/blob/1151ccc7ff2f4d7635c156e719739b67ad18616e/website/dev_stratlabs/src/constants.js#L2" target="_blank">`WORDS`</a> is defined in <a href="https://github.com/JoeNatan30/SpanishToPSLTranslationService/blob/1151ccc7ff2f4d7635c156e719739b67ad18616e/website/dev_stratlabs/src/constants.js" target="_blank">`constants.js`</a>, housing the URL of the AWS API Gateway. This URL follows the format of <a href="https://xxxxx.execute-api.us-east-1.amazonaws.com/" target="_blank">`https://xxxxx.execute-api.us-east-1.amazonaws.com/`</a>.

To conveniently access the API Gateway Service within your AWS account for API creation (using the HTTP protocol), employ the provided ID to construct the API URL mentioned earlier.

For the API we crafted, the following configuration was employed:

- API type: HTTP
- Authorization: NONE
- CORS: Yes
- Detailed metrics enabled: No
- Method: ANY
- Service principal: apigateway.amazonaws.com
- Stage: $default



<!-- The "Text Search" feature resides within its dedicated directory named [`text-search`](https://github.com/JoeNatan30/SpanishToPSLTranslationService/tree/main/website/dev_stratlabs/src/features/text-search), which encapsulates all the necessary components, views, routes, and services required for text-based searches. The directory consists of three primary sections: *components*, *views*, and *support files*. The components folder contains reusable UI elements, particularly hosts the component required for text search by the AI result. While the views folder houses the [`TextSearch.jsx`](https://github.com/JoeNatan30/SpanishToPSLTranslationService/blob/main/website/dev_stratlabs/src/features/text-search/views/TextSearch.jsx) where users interact with the [search functionality](https://dvlsp.link/search-by-text). The feature's routes are defined in [`text-search.routes.js`](https://github.com/JoeNatan30/SpanishToPSLTranslationService/blob/main/website/dev_stratlabs/src/features/text-search/text-search.routes.js), ensuring seamless navigation. Furthermore, the feature leverages [`text-search.services.js`](https://github.com/JoeNatan30/SpanishToPSLTranslationService/blob/main/website/dev_stratlabs/src/features/text-search/text-search.services.js) to manage the logic for performing text searches. This structured organization promotes code reusability, encourages a clean separation of functionalities, and simplifies collaborative development efforts.

The implementation of [`TextSearch.jsx`](https://github.com/JoeNatan30/SpanishToPSLTranslationService/blob/main/website/dev_stratlabs/src/features/text-search/views/TextSearch.jsx) feature is responsible for handling text-based searches. Users can input keywords to search for information. The component utilizes states to manage the search state, messages, and results. It also has the capability to implement an autocomplete system (currently disabled). Furthermore, it handles search-related actions, including error management and notifications.

The [`TextSearch`](https://github.com/JoeNatan30/SpanishToPSLTranslationService/blob/1151ccc7ff2f4d7635c156e719739b67ad18616e/website/dev_stratlabs/src/features/text-search/views/TextSearch.jsx#L62C10-L62C20) component encapsulates the logic for performing searches when users input a search query and click the "Search" button. The `onSearch` function is triggered in response to this event. This function leverages the `textSearchService` to execute the search, update results and messages based on the outcomes, and manage errors.

The component also includes JSX code to render the search modal and display results in cards (Card components). The results are mapped using `results.map` and rendered in cards. These results are what will be displayed on the search page.

The visual aspect of the component is constructed using components from Material Kit 2 (MK2) for React. These components are utilized to build the user interface and style the elements on the page.

Here's how the imports correlate with the code:

- `MKBox`: This component is used to create flexible containers and apply styles to different sections of the interface.
- `MKTypography`: This component is used to render text with custom typography styles.
- `MKButton`: This component is used to create buttons with custom styles.
- `MKInput`: While this component is not present in the provided code, I assume it is used to create input fields with customized styles.

Overall, Material Kit 2 components enhance the appearance and usability of the user interface by providing cohesive and visually appealing elements.

### Integration with `textSearchService`

`TextSearch` makes use of the `textSearchService` service, which is referenced from [`text-search.services.js`](https://github.com/JoeNatan30/SpanishToPSLTranslationService/blob/main/website/dev_stratlabs/src/features/sign-search/sign-search.services.js).

The `text-search.services.js` service encapsulates the logic for conducting text searches using an external API (utilizing Axios). It takes a search query and an index type as input, makes a request to the API, transforms the results into a customized format, and then returns them.

The API request is made to the URL defined in [`API_URLS.WORDS`](https://github.com/JoeNatan30/SpanishToPSLTranslationService/blob/1151ccc7ff2f4d7635c156e719739b67ad18616e/website/dev_stratlabs/src/utils/axios-config.js#L5), which is provided in the `axios-config` file. With `baseURL: API_URLS.WORDS` configured in Axios, all requests made using the Axios client (API) will have the URL defined in `WORDS` as their base.

The constant [`WORDS`](https://github.com/JoeNatan30/SpanishToPSLTranslationService/blob/1151ccc7ff2f4d7635c156e719739b67ad18616e/website/dev_stratlabs/src/constants.js#L2) is defined in `constants.js`, where the URL of the AWS API Gateway is specified. This URL follows the format of `https://xxxxx.execute-api.us-east-1.amazonaws.com/`.

You can conveniently access the API Gateway Service within your AWS account to establish the API (in this instance, using the HTTP protocol). The provided ID is employed for constructing the aforementioned API URL.

We have employed the subsequent configuration for the API we created:

API type: HTTP
Authorization: NONE
CORS: Yes
Detailed metrics enabled: No
Method: ANY
Service principal: apigateway.amazonaws.com
Stage: $default -->

<!-- Para las conexiones por Backend entre la web y las funcionalidades para el Text Search, es necesario realizar una conexión entre los servicios DynamoDB y la web. Para ello nos servimos del servicio de AWS Lambda para poder realizar un trigger definidio (en este caso la búsqueda de texto) para llamar a este lambda y este se encargue de redireccionar a DynamoDB. Adicionalmente, cabe recalcar que se tuvieron que considerar 2 situaciones de búsqueda por texto. La búsqueda por texto per se , y una "búsqueda por texto" , donde en realidad el resultado de la AI en la búsqueda por seña, toma la palabra resultado y hace la búsqueda en DynamoDB para poder obtener el vídeo ejemplo de la seña que se predijo.

## AWS Lambda Implementation
Por tanto tenemos 1 lambda que cumplirá dos situaciones. Es requerido para este paso que se modifique DynamoDB añadiendo un indexName para hacer referiencia a esta otra situación. Para ello hacemos uso de 2 scripts dentro del Lambda buil-word-query.js y handler.js

Nombre de AWS Lambda: sign-language-dev-queryWord

**handler.js:**

The handler.js file is a module responsible for handling incoming requests to the AWS Lambda function. It interacts with DynamoDB, to retrieve data based on provided parameters.

The core functionality is encapsulated within the getItemsByWord function. This function accepts a word and an indexType as parameters. Depending on the indexType, it constructs a query using the appropriate query building function from the 'build-word-query.js' module. The constructed query is then executed against the DynamoDB table using the AWS.DynamoDB.DocumentClient() instance, either through a query or a scan operation, based on the presence of the word parameter.

The exported queryWord async function is designed to be the entry point for Lambda invocations. It extracts the 'word' and 'indexType' parameters from the incoming event's queryStringParameters and calls the getItemsByWord function to retrieve relevant items from the DynamoDB table. The successful result is then wrapped in an HTTP response using the success function from the 'shared/responses' module.

**build-word-query.js:**

The build-word-query.js module contains query building functions used by the handler.js module. These functions construct DynamoDB queries for specific search scenarios. The module exports two functions, namely buildWordQuery and buildIAResultsQuery.

The buildWordQuery function is designed to create a query that searches for a 'gloss' in the DynamoDB table. It constructs a query with the appropriate parameters, including the TableName, KeyConditionExpression, ExpressionAttributeNames, and ExpressionAttributeValues.

The buildIAResultsQuery function is used to construct a query specifically for the index 'sign_gloss_var-index' within the 'gloss' table. This function sets up the query with the necessary parameters for efficiently searching indexed data.

Both query building functions accept a 'word' parameter and use it to create a query that matches the provided word value. 


## React Implementation
 -->

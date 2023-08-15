# How it Works
## Overview
### User Perspective

Navigating through the Dictionary LSP from a user's perspective is straightforward and user-friendly. The user journey can be summarized in a few simple steps:

1. **Access the Website**: Begin by accessing the Dictionary LSP website.
2. **Search and Record**: For text-based searches, simply enter your desired search term and click the search button. Alternatively, for video-based searches, grant the necessary camera permissions and record a short 5-10 seconds video. This video will be used for machine learning inference.
3. **Obtain Search Results**: Receive the search results, which may include recognized signs or a list of similar LSP signs based on your search query.
4. **Learn LSP**: Engage with the search results and expand your knowledge of LSP as you explore the captured signs and their meanings.

### React Integration: Connecting Front-end and Back-end

The React Integration serves as a bridge connecting the front-end and back-end functionalities. It's responsible for receiving data retrieved from various AWS services and presenting it to users through the user interface. This integration encompasses both the user interface design and the underlying JavaScript functionalities.

Front-end and back-end activities are intertwined within the React Integration, ensuring a seamless and responsive experience:

- **Front-end Structure**: Utilizing Material Kit 2 (MK2) components for React, the front-end structure is built to provide an appealing and user-friendly interface. MK2 components, such as `MKBox`, `MKTypography`, `MKButton`, and `MKInput`, play a pivotal role in shaping the UI and applying customized styles to elements.

- **AWS Data Retrieval**: The React Integration receives data retrieved from AWS services, such as Amazon S3, Lambda, and DynamoDB. This data may include video files, search results, or other relevant information.

- **Dynamic Rendering**: React's dynamic rendering capabilities enable real-time updates and content rendering without requiring full page reloads. This ensures that users receive instant feedback on their actions and queries, enhancing the overall responsiveness of the website.

### Backend Configuration and AWS Services

The backend configuration of the Dictionary LSP involves setting up and utilizing various AWS services to enhance the functionality and efficiency of the system:

1. **AWS Account Setup**: Create an AWS account and configure AWS credentials to enable seamless interactions with AWS services.

2. **S3 Bucket Configuration**: Establish S3 buckets to store assets such as video files. Ensure proper bucket configuration to facilitate efficient data storage and retrieval.

3. **Model Deployment and Inference**: Deploy the trained machine learning model using Amazon SageMaker, as detailed in the [`Inference README`](https://github.com/Diccionario-LSP/SpanishToPSLTranslationService/tree/main/Inference). Use Lambda functions to create an intermediary between the web and the back-end for efficient communication.

4. **Database Interaction**: Configure Lambda functions to interact with databases like RDS or DynamoDB. These interactions facilitate efficient text searches and data management, providing accurate search results.

Navigating through the Dictionary LSP involves engaging with the React Integration, which seamlessly connects the front-end user interface with the back-end AWS services. As users interact with the intuitive interface and receive dynamic search results, the integrated system ensures a smooth and informative experience.

---

## Deployment of Dictionary LSP
The deployment of the Dictionary LSP consists of three core modules: Website, Sign Search, and Text Search. Each module contributes to the functionality of the dictionary, enabling users to access and explore Peruvian Sign Language (LSP) signs efficiently. 

The architecture diagram below provides a visual overview of the interplay between the three modules:

<p align="center">
  <img src="./assets/AWS_Diagram_v2.png" alt="Architecture" width="738">
</p>


### Website Module
The Website module encompasses the front-end user interface and complementary backend functionalities. Built using JavaScript and React, the website provides a visually engaging experience for users. It offers features like webcam access, integration with AWS services, and real-time pose estimation through third-party APIs like Mediapipe. The website leverages AWS services such as Route 53, S3 Bucket, and CloudFormation.

### Sign Search Module

The Sign Search module is a critical and intricate component of the architecture, enabling users to perform accurate and efficient searches for Peruvian Sign Language (LSP) signs based on video input. This module seamlessly integrates various technologies and services to deliver a seamless experience. Here's a comprehensive overview of how the Sign Search module operates:

* **Model Deployment and Inference**: The foundation of the Sign Search module lies in the accurate recognition and classification of signs within streaming videos. This is achieved through the deployment of a Sign Language Recognition (SLR) model. The details of the model deployment and inference process are extensively covered in the dedicated [`Inference repository`](https://github.com/Diccionario-LSP/SpanishToPSLTranslationService/tree/main/Inference). In summary, the SLR model is developed using Python and is trained to recognize and classify signs in real-time video streams. The model's accuracy and performance are essential to providing users with meaningful search results.

* **Backend Connections and React Integration**: Once the SLR model successfully recognizes signs within video streams, the results are communicated to the website's backend using AWS Lambda services. This integration is crucial to ensure smooth communication between the model and the user interface. AWS Lambda functions are triggered by the model's output and subsequently update the website's state. This process enables the website to display the recognized sign or a list of the top five similar LSP signs. The communication between the backend and the frontend is established using React, a widely-used JavaScript library. More details about this communication is in [`React Integration Section`](#react-integration).

Additionaly, the Sign Search module include a connection to the Text Search module through a similar functionality of text-search in order to show the sample videos for the AI results after the inference. This functionality use the same Lambda function as [`Text Search`](#text-search-module) module but with a different indextype parameter, in order to enable a different output, showing a single video for the specific glosa predicted.

The Sign Search module leverages several AWS services to ensure its functionality such as Sagemaker, Lambda, S3 and API Gateway.

The collaboration of these AWS services, the SLR model, and the React-based frontend ensures the Sign Search module's effectiveness in providing accurate, timely, and relevant sign search results. This module significantly enhances the Dictionary LSP's usability and empowers users to explore and learn from LSP videos effectively.


### Text Search Module:
The Text Search module facilitates video retrieval based on textual queries. It encompasses the necessary services to execute text-based searches within the LSP video database. The database, hosted in AWS DynamoDB, contains vital information required to retrieve relevant videos based on specific criteria. Users can perform searches using either a word-based query (Search by Word) or a gloss-based query (Sign Search AI Video Search) resulting from AI-based inference. This module relies on various AWS services, including API Gateway, Lambda, RDS/DynamoDB, and S3, to ensure accurate and swift retrieval of videos that match the search criteria.

The integration of these modules enables the Dictionary LSP to deliver a comprehensive and user-friendly experience, allowing individuals to learn and explore Peruvian Sign Language efficiently. The combination of AI-driven sign recognition, text-based search, and an intuitive web interface empowers users to engage with and understand LSP in a meaningful way.

---

## React Integration

React plays a pivotal role in ensuring a seamless and user-friendly experience within the Dictionary LSP's user interface. By integrating React, the website becomes capable of dynamically handling data updates and rendering content in real-time, all without requiring a full page reload. This integration significantly enhances the overall responsiveness and usability of the website, ensuring that users receive instant feedback on their sign search queries.

One of the key advantages of using React is its ability to manage state efficiently. The website's state, which encompasses the data being displayed and user interactions, is effectively managed by React components. This allows for smooth updates and re-rendering of specific parts of the interface as new data becomes available. As a result, users can witness immediate changes on the screen, providing a fluid and intuitive experience.

To enhance the visual appeal and usability of the user interface, Material Kit 2 (MK2) components for React are employed. These components serve as building blocks for constructing the UI and applying styling to the page's elements. The integration of MK2 components aligns with modern design principles, ensuring that the website's appearance is both aesthetic and functional.

Here's a breakdown of how the imported MK2 components correspond to the code:

- `MKBox`: This versatile component facilitates the creation of flexible containers and allows for the application of customized styles to various sections of the interface. It aids in structuring the layout and organizing different content components.

- `MKTypography`: Designed specifically for rendering text with tailored typography styles, this component ensures consistent and visually pleasing text throughout the website. It enables the implementation of varying font styles and sizes to suit different parts of the content.

- `MKButton`: The `MKButton` component is utilized for creating buttons with personalized styles. Buttons are essential for user interactions, and the ability to customize their appearance enhances the overall visual coherence and user experience.

- `MKInput`: Although not explicitly present in the provided code, I assume the `MKInput` component is employed to generate input fields with distinctive styles. Input fields are commonly used for user inputs and search queries, and customizing their appearance contributes to the overall design consistency.

For a more detailed understanding of how React is integrated within the Dictionary LSP's user interface, along with the utilization of Material Kit 2 (MK2) components, you can refer to the dedicated [Stratlabs Template](https://github.com/Diccionario-LSP/SpanishToPSLTranslationService/tree/main/website/dev_stratlabs_template). This template provides insights into the project structure, React integration, and the application of MK2 components for enhanced UI design and interactivity.


<!-- El deployment del Diccionario Web esta compuesta por 3 módulos, Website, Sign Search, y Text Search. En la Website como tal esta alojado el Front End y funcionalidades de backend basadas en Javascript y React, Sign Search implica tanto la inferencia del modelo de reconocimiento de lengua de señas (SLR) como el backend relacionado que conecta la inferencia con la website usando servicios basados en React. Text Search implica Data Processing and Upload Workflow en los servicios de AWS DynamoDB 
<p align="center">
  <img src="./assets/AWS_Diagram_v2.png" alt="Architecture" width="738">
</p>

* **Website**:  en la web lo que ocurre es el aspecto visual del diccionario web y funcionalidades complementarias de acceso a la webcam, conexión con AWS, y estimación de pose a través de APIs de terceros (Mediapipe). Usa los servicios de AWS de Route S3, S3 Bucket para alojar la web y CloudFormation

* **Sign Search**: esta comprende tanto la inferencia,Probably the most import module of our architecture which realizes inferences of video streaming from the website and return as a result the sign or a top 5 of similar signs of LSP (Lengua de Señas Peruana), como los servicios que permiten el deployment del modelo de SLR y sus conexiones con la Web a través del Backend usando React y Servicios de AWS como Sagemaker, Lambda, S3 , API Gateway

* **Text Search**: Comprende tanto los servicios necesarios para realizar una búsqueda por texto, incluyendo la base de datos alojada en DynamoDB con la información requerida para hacer la búsqueda de los vídeos de la bbdd en función a una búsqueda en base a una palabra (Search by word and show LSP videos related to that word ) o en base a una glosa resultado de la inferencia de IA (Sign Search AI Video Search). [Services used: API Gateway, Lambda, RDS/DynamoDB, S3] -->


<!-- 
### **User perspective**
1. Enter to the website
2. For text input, enter your search and then click search. For video input, allow the permissions of your camera and record a 5-10 seconds video for ML inference
3. Receive the result of text/video search
4. Learn LSP!

### **Front-end perspective**
1. Once the are ready, there is necessary to upload them to S3, then the S3 bucket is configured to public access, Route 53 and Cloud Formation have to be configured with records info of the hosted domain.

### **Back-end perspective**
1. After the training and local test of the ML model there is necessary to create an aws account, configure the AWS credentials, and create a S3 Bucket for the model deployment and video uploads.
2. Make sure the buckets are configured to allow required operations.
3. Deploy the model using Sagemaker as described in [`Inference README`](https://github.com/Diccionario-LSP/SpanishToPSLTranslationService/tree/main/Inference).
3. The next step is create a Lambda function to call Sagemaker as a intermediary between the web and the back-end.
4. Additionally is necessary configure Lambda functions for text search and the Databases in RDS/DynamoDB. -->
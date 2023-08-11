# ConnectingPoints

In this stage, pose estimation models are used to process the data extracted from the prepared and organized videos after the annotation and preprocessing stage. Three models, namely Mediapipe, OpenPose, and Wholepose, are utilized, which can be used together or independently as required.

For installation instructions for each model, please refer to the "Keypoint estimator models installation" section in the [Connecting Points](https://github.com/JoeNatan30/ConnectingPoints) repository.

This documentation specifically explains how ConnectingPoints can be used with the datasets previously worked on, including PUCP305.

The following diagram summarizes this stage, where the expected output is an HDF5 file.

<p align="center">
  <img src="./assets/ConnectingPoints.png" alt="ConnectingPoints" width="738">
</p>


The output of the ConnectingPoints stage will be an HDF5 file (.hdf5) with the following structure:

Each group in the HDF5 file corresponds to an index (e.g., "#1", "#2", etc.) and contains the following information:

- ["video_name"]: The name of the video, including the relative path starting after the name of the dataset.
- ["label"]: The isolated signs shown in the video.
- ["data"]: A TxCxK structure, where:
  - T: The timestep of the video.
  - C: The x and y coordinates.
  - K: The keypoints, including "pose", "face", "left hand", and "right hand".

The output HDF5 file will have separate groups for each keypoint estimator used and each dataset or mix chosen. The user needs to create the "output" folder to store the generated HDF5 files.

The ConnectingPoints stage will process the datasets and keypoints estimators to produce the HDF5 files, which will contain the pose estimation data, video names, and corresponding labels or glosses.

For more details about the implementation of this step, please refer to the [Connecting Points](https://github.com/JoeNatan30/ConnectingPoints) repository.


<!-- En esta etapa se hará uso de modelos de estimación de pose, y el tratamiento de esta data extraida a partir de los vídeos preparados y organizados luego de la etapa de anotación y preprocesamiento.

Para ello se hace uso de 3 modelos, Mediapipe, OpenPose y Wholepose, los cuales pueden ser usados tanto en conjunto como independientemente según corresponda.

The installation instructions for each model are given in the "Keypoint estimator models installation" section en el repositorio [Connecting Points](https://github.com/JoeNatan30/ConnectingPoints).

La presente documentación específica como puede este ser usado en función a las bases de datos con las que fue trabajado anteriormente (incluyendo PUCP305)

El siguiente diagrama resume esta etapa, en el cual se espera tener un HDF5 file como output

The output of the ConnectingPoints stage will be an HDF5 file (.hdf5) with the following structure:

Each group in the HDF5 file corresponds to an index (e.g., "#1", "#2", etc.) and contains the following information:

["video_name"]: The name of the video, including the relative path starting after the name of the dataset.
["label"]: The isolated signs shown in the video.
["data"]: A TxCxK structure, where:
T: The timestep of the video.
C: The x and y coordinates.
K: The keypoints, including "pose", "face", "left hand", and "right hand".
The output HDF5 file will have separate groups for each keypoint estimator used and each dataset or mix chosen. The user needs to create the "output" folder to store the generated HDF5 files.

The ConnectingPoints stage will process the datasets and keypoints estimators to produce the HDF5 files, which will contain the pose estimation data, video names, and corresponding labels or glosses.

More details about the implementation of this step is done in [Connecting Points](https://github.com/JoeNatan30/ConnectingPoints)
 -->

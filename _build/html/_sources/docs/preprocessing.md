# Annotation and Isolating Sign Language Videos

In this stage, both video preparation and the annotation of signs corresponding to the videos are performed, as shown in the following workflow.

## Annotation of Signs

For the annotation of videos, we utilized the ELAN video annotation tool, where previously agreed-upon glosses for the words described in each example sentence from the PUCP305 database were annotated. The PUCP305 database contains multiple examples of sentences, each containing several signs for different words. The task was to create an Isolated Sign Language (ISL) video database. The annotation of glosses, along with their corresponding timeframes, served as the basis for creating the ISL database, as illustrated in the image below. This task involved meticulous work from the entire linguistic team and the Deaf community to create a dataset that accurately represents how communication occurs within the community, requiring in-depth study to define the signs' glosses correctly.

<p align="center">
  <img src="./assets/ELAN.jpg" alt="Annotation ELAN" width="560">
</p>


## Video Preparation

Video preparation involved using Python scripts for specific preprocessing tasks, divided into three stages: transforming EAF (ELAN files) into SRT files, extracting all videos from the unprocessed database, and then cropping them based on the timeframes indicated in the SRT files to generate each ISL video. Additionally, if needed, cropping was performed to include only the interpreter's location (used in previous databases, where the interpreter was not centered in the video, and the video contained content besides the interpreter).

<p align="center">
  <img src="./assets/Preprocessing.png" alt="Preprocessing" width="738">
</p>

### Step 1: Transforming ELAN to SRT Files

The "transformEAFTtoSRT.py" script aims to convert ELAN (EAF) files containing gloss annotations into SubRip (SRT) subtitle files and generate a vocabulary count. The script processes all ELAN files found in a specified folder and its subfolders. It reads the gloss annotations from the ELAN files and creates corresponding SRT subtitle files. The script also counts the occurrences of each gloss annotation and generates a vocabulary count, saving it to a CSV file named 'glosas.csv'.

### Step 2: Video Extraction

The "move_videos.py" script aims to copy video files referenced in ELAN (EAF) files from their original location to a specific destination folder. The script recursively searches for ELAN files with the '.eaf' extension and, for each file found, locates and copies the associated video file. If the video file is not found at the original location, the script looks for it in the linked media list of the ELAN file and corrects the video's path if necessary before performing the copy. This script ensures that the videos are correctly organized in the destination folder, which facilitates further processing or analysis. Note that the destination folder is typically located within the <a href="https://github.com/gissemari/PeruvianSignLanguage" target="_blank">`PeruvianSignLanguage`</a> repository in the following structure:


<!-- En esta etapa se realiza tanto la preparación de los vídeos como la anotación de las señas correspondientes a los vídeos, como se muestra en el flujo a continuación.

Anotación de Señas

Para la anotación de los vídeos se hizo uso de la herramienta de anotación de videos ELAN, donde se anotaron glosas previamente acordadas para las palabras descritas en cada oración ejemplo de la palabra correspondiente de acuerdo a la base de datos PUCP305, es decir, la base de datos cuenta con múltiples ejemplos de oraciones, y en cada oración se tienen múltiples señas de las diferentes palabras, la tarea es crear un base de datos de Isolated Sign Language (ISL) videos, por tanto la anotación de la Glosa en conjunto con los timeframes donde ocurren sirvieron para la creación de ISL database, como se muestra en la siguiente imagen.
Esta tarea fue un trabajo arduo realizado por todo el equipo de lingüística y la comunidad sorda para crear un conjunto de datos que sean acordes a como se comunican en la comunidad, requiriendo un arduo estudio para definir correctamente las glosas de las señas.

Preparación de Videos

Para la preparación de los vídeos se hizo uso de scripts en python para la realización del preprocesamiento correspondiente, dividido en 3 etapas, transformar los EAF (ELAN files) en archivos SRT , que son mejor manejables para la tarea, extraer todos los vídeos de la base de datos no procesada , y luego recortarlos en función a los timeframes indicados en los archivos SRT para poder generar cada ISL video, asimismo si correspondía se realizaba un recorte de la ubicación del intérprete (utilizado en base de datos anteriores, donde el intérprete no se ubicaba en el centro del vídeo y el vídeo mostraba contenido además del intérprete)

Paso 1: Transformación de ELAN to SRT files

El archivo "transformEAFTtoSRT.py" tiene como objetivo convertir archivos ELAN (EAF) que contienen anotaciones de glosas en archivos de subtítulos SubRip (SRT) y generar un recuento de vocabulario. El script procesa todos los archivos ELAN encontrados en una carpeta especificada y sus subcarpetas. Lee las anotaciones de glosas de los archivos ELAN y crea archivos de subtítulos SRT correspondientes. También cuenta las ocurrencias de cada anotación de glosa y genera un recuento de vocabulario, que se guarda en un archivo CSV llamado 'glosas.csv'. Para utilizar el script, asegúrese de tener instaladas las bibliotecas requeridas y establezca las rutas de entrada y salida según corresponda. Después de ejecutar el script, encontrará los archivos SRT procesados y el recuento de vocabulario en la carpeta de salida especificada.

Paso 2: Extracción de los videos

El archivo "move_videos.py" tiene como objetivo copiar archivos de video referenciados en archivos ELAN (EAF) desde una ubicación original a una ubicación de destino específica. El script recorre una estructura de directorios para buscar archivos ELAN con extensión ".eaf" y, para cada archivo encontrado, busca y copia el archivo de video asociado. Si el archivo de video no se encuentra en la ubicación original, busca en la lista de medios vinculados del archivo ELAN y corrige la ruta del video si es necesario antes de realizar la copia. El script asegura que los videos estén correctamente ubicados en la carpeta de destino, lo que facilita su posterior procesamiento o análisis. Tomar en cuenta, que por lo general la carpeta de destino deberá encontrarse en el repositorio [PeruvianSignLanguage](https://github.com/gissemari/PeruvianSignLanguage) con la siguiente estructura: -->

```
PeruvianSignLanguage
.
.
.
├── Data
    ├── PUCP305 [subfolder for PUCP305 database]
    │   ├── SRT                        [subfolder containing SRT files]
    │   │   ├── SRT_SEGMENTED_SIGN
    │   │   │   ├── ABRIR_1.srt
    │   │   │   └── ABRIR_2.srt
    │   ├── Videos                      [subfolder containing mp4 files]
    │   │   ├── Original
    │   │   │   ├── ABRIR_1.mp4
    │   │   │   └── ABRIR_2.mp4

```


### Step 3: Video Segmentation and Cropping

The video segmentation and cropping process is a critical step for isolating specific segments from the original videos. To achieve this, we have developed the `cropInterpreterBySRT.py` script. 

This script is responsible for segmenting video segments based on annotations provided in SRT files. It extracts temporal information from the SRT files, allowing it to identify the start and end times of each segment of interest.

The script then creates new videos with the timeframes desired for each isolated sign. Users have the flexibility to customize the segmentation, including setting the dimensions of the sign language interpreter (specifying a region of interest for cropping), and adjusting the speed of the segmented videos.

It is important to ensure that the SRT files and corresponding video files are located in the paths specified by "--srtPath" and "--rawVideoPath," respectively. It is assumed that these files are within the <a href="https://github.com/gissemari/PeruvianSignLanguage" target="_blank">`PeruvianSignLanguage`</a> repository in the "Data" folder.

For a more detailed guide on using this script and its various options, please refer to the [Dictionary LSP Documentation](https://github.com/Diccionario-LSP/Docs).

<!-- Paso 3: Recorte de los vídeos


El archivo "cropInterpreterBySRT.py" tiene como objetivo segmentar y recortar segmentos específicos de videos basándose en las anotaciones proporcionadas en archivos SRT. Para ello, el script lee los archivos SRT ubicados en la ruta especificada por "--srtPath" y utiliza la información temporal de las anotaciones para identificar los momentos de inicio y finalización de cada segmento de interés.

El script utiliza la biblioteca pysrt para leer los archivos SRT y extraer los tiempos de inicio y finalización de cada anotación. Luego, utiliza la biblioteca cv2 para leer los videos de origen ubicados en la ruta especificada por "--rawVideoPath". Posteriormente, para cada segmento identificado en el archivo SRT, el script recorta el video original correspondiente y crea un nuevo video que contiene solo el segmento recortado. Los nuevos videos segmentados se guardan en la ruta especificada por "--outputVideoPath".

El script también proporciona opciones adicionales para personalizar la segmentación, como la posibilidad de establecer el ancho y alto del intérprete de lenguaje de señas en los videos segmentados y la opción de recortar el video para incluir solo una región de interés específica (coordenadas "x1", "y1", "width" y "height"). Además, se puede controlar la velocidad de los nuevos videos segmentados ("fpsOutput") y la opción de considerar el gesto en el nombre de salida del video.

Es importante tener en cuenta que para que el script funcione correctamente, es necesario que los archivos SRT y los archivos de video correspondientes se encuentren en las ubicaciones especificadas por "--srtPath" y "--rawVideoPath", respectivamente. Se supone que estos archivos se encuentran dentro del repositorio [PeruvianSignLanguage](https://github.com/gissemari/PeruvianSignLanguage) en la carpeta "Data". -->

<!-- ### Repository for cropInterpreterbySRT.py, move_videos.py, transformEAFtoSRT.py

### and for details about annotation process using ELAN software -->


# 16S-rRNA-Metagenomics---Ion-Torrent
Este flujo de trabajo está basado en la metodología descrita por Maki, K. A. y colaboradores (2023), en el artículo títulado "Multi-amplicon microbiome data analysis pipelines for mixed orientation sequences using QIIME2: Assessing reference database, variable region and pre-processing bias in classification of mock bacterial community samples"

## Separate sequences by V Region
Existen algoritmos como "Cutadapt" (M. Martin, 2011) que realizan la labor de eliminar los primers añadidos en la preparación de las bibliotecas, sin embargo, para esto es necesario conocer la secuencia de los primers, los cuales no son públicos para el "Ion 16S™ Metagenomics Kit". Por lo tanto, es necesario utilizar el plugin "MetagenomicsPP" versión 2.1.14, el cual es una modificación hecha por ThermoFisher del algoritmo de "Cutadapt" original. Descargado de: https://apps.thermofisher.com/apps/spa/#/publiclib/plugins. 
Ejecutar el plugin en consola de comandos, llamado "mgpp.sh". Antes de esto, es necesario crear un archivo manifest format, en formato .tsv o .txt, el cual contiene dos columnas, indicando el ID que se le otorgará a cada muestra, y la segunda conteniendo su ruta absoluta.

| sample-id |  | absolute-filepath |
|-----------|--|-------------------|
| PS1 | | /home/IsaacPlastomas/AndruMetagenomica/R_2020_05_06_13_54_04_user_CUC-33-Metagenomica_lemitax_060520.IonXpress_001.fastq |
| PS2 |	| /home/IsaacPlastomas/AndruMetagenomica/R_2020_05_06_13_54_04_user_CUC-33-Metagenomica_lemitax_060520.IonXpress_002.fastq |
| PS3 |	| /home/IsaacPlastomas/AndruMetagenomica/R_2020_05_06_13_54_04_user_CUC-33-Metagenomica_lemitax_060520.IonXpress_003.fastq |
BT1	/home/IsaacPlastomas/AndruMetagenomica/R_2020_05_06_13_54_04_user_CUC-33-Metagenomica_lemitax_060520.IonXpress_004.fastq
BT2	/home/IsaacPlastomas/AndruMetagenomica/R_2020_05_06_13_54_04_user_CUC-33-Metagenomica_lemitax_060520.IonXpress_005.fastq
BT3	/home/IsaacPlastomas/AndruMetagenomica/R_2020_05_06_13_54_04_user_CUC-33-Metagenomica_lemitax_060520.IonXpress_006.fastq
L1I	/home/IsaacPlastomas/AndruMetagenomica/R_2020_05_06_13_54_04_user_CUC-33-Metagenomica_lemitax_060520.IonXpress_007.fastq
L2I	/home/IsaacPlastomas/AndruMetagenomica/R_2020_05_06_13_54_04_user_CUC-33-Metagenomica_lemitax_060520.IonXpress_008.fastq
L3I	/home/IsaacPlastomas/AndruMetagenomica/R_2020_05_06_13_54_04_user_CUC-33-Metagenomica_lemitax_060520.IonXpress_009.fastq
L1V	/home/IsaacPlastomas/AndruMetagenomica/R_2020_05_06_13_54_04_user_CUC-33-Metagenomica_lemitax_060520.IonXpress_010.fastq
L2V	/home/IsaacPlastomas/AndruMetagenomica/R_2020_05_06_13_54_04_user_CUC-33-Metagenomica_lemitax_060520.IonXpress_011.fastq
. . .

```bash
sh MetagenomicsPP/mgpp.sh -m manifest_lemitax.txt -o mgpp_output_vregions
```

Una vez separada cada muestra en sus regiones hipervariables, reorganizar todas las muestras en carpetas para su respectiva región - i.e., extraer cada FastQ de su carpeta original para cada muestra, y reubicarla en una nueva carpeta madre donde se encontrarán todas las muestras correspondientes a dicha región. Para realizar esta tarea, se realizó el siguiente script en python. Este paso es necesario, ya que a la hora de importar las muestras a QIIME2, se importarán como un conjunto de samples, pero esto es necesario hacerse individualmente para cada región.

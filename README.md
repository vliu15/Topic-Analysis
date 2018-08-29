# ELMo Embeddings for Medica transcriptions
To convert Optum's Medica speech transcriptions to ELMo embeddings per sentence for clustering.


## Requirements:
 * Python3 (>=3.6 for AllenNLP)
 * AllenNLP
 * TensorFlow
 * NumPy
 * SKLearn (for clustering)
 * Torch (for AllenNLP)
 * SciPy (for SKLearn stop words)


## Install
To install the necessary dependencies:

```bash
apt-get install python3-pip
pip3 install allennlp
pip3 install tensorflow-gpu
pip3 install numpy
pip3 install sklearn
pip3 install torch
# pip3 install scipy
```


## Usage
To generate sentence embeddings, make sure that the `sentences.txt` file is formatted as such:

```
<sentence/transcription>
<sentence/transcription>
# and so on...
```

Run `python3 main.py` with the following options:
 * `--mode embed` to embed the sentence file
 * `--mode sif` to enhance sentence embeddings with SIF
 * `--mode cluster` to cluster embeddings
 * `--mode project` to reduce dimensionality for visualization
 * `--mode metadata` to write metadata file
 * `--mode tensorboard` to create TensorBoard files

Adjust runtime flags in `main.py`

A couple auxiliary files:
 * Run `sh clean.sh` to convert transcriptions to lower case and remove stop words
 * Run `python3 remove.py` to remove clusters (specified in program)

## Run
To run inside a Docker container:

```bash
docker build -t elmo-embeddings .
docker exec -it elmo-embeddings /bin/bash
python3.6 main.py
```

To run on LSF:

```bash
bsub -o output.txt \
     -env LSB_CONTAINER_IMAGE=docker.optum.com/dl_lab/elmo:tf-gpu \
     sh -c "cd /data/elmo-embedding && python3.6 main.py --mode <mode>"
```


## Outputs
An output folder will be created in the current directory containing:
 * `embeddings.npy`: a NumPy array of sentence embeddings (NumPy arrays) in binary format
 * `embeddings_sif.npy`: a NumPy array of sentence embeddings after SIF
 * `embeddings_pc.npy`: a NumPy array of sentence embeddings after PCA
 * `embeddings_ts.npy`: a NumPy array of sentence embeddings after t-SNE
 * `km_labels.json`: a list of cluster labels generated by KMeans
 * `metadata.tsv`: metadata of sentence labels for visualization

Other nested output folders:
 * `tensorboard`: for TensorBoard output logs
 * `kmeans`: for clustered sentences with kmeans
 * `trimmed`: for another copy of embeddings/sentences with specified clusters removed
 * `hierarchy`: for clustered sentences with hierarchical kmeans


## Results
On a dataset of 48k transcriptions / 137k sentences, the following process produced relatively defined clusters:

```
ELMo -> layer 2 (per word) -> average (per sentence) -> SIF -> KMeans (k=100)
t-SNE (7.5k iterations, for visualization)
```

To improve/fix:
 * Allow ELMo to use GPU for embedding (should just be installing PyTorch compatible with CUDA 9.0)
 * Achieve more defined clusters


## TO-DO
 - [x] Preprocess transcriptions
 - [x] Embed each sentence with ELMo
 - [x] Enhance embeddings with SIF
 - [x] Cluster using SKLearn KMeans (optional: hierarchically)
 - [x] Find optimal k using elbow method and silhouette scores (optional)
 - [x] Reduce dimensionality for visualization (PCA, t-SNE)
 - [x] Run in TensorBoard
 - [ ] Conclusions

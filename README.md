# IDP Radio

## Getting Started

1. Clone this repository

2. Use the `download_dataset.sh` script to download the datasets you required. The default behavior of the script will result in downloading the "Chexpert Dev Dataset" and the "Chextxray14-NIH_256" dataset. Additional information about the datasets can be found in the wiki under [Data Set & Preprocessing Methods](https://git.veios.cloud/idp1-radio/idp-radio-1/-/wikis/Data-Set-&-Preprocessing-Methods)
  - Use `-f` to overwrite the existing dataset
  - Use `-s` to download the server versions ("Chexpert Full Dataset" and "Chextxray14-NIH_256")
  - Use `-a` to download all datasets and all their versions ("Chexpert Dev Dataset", "Chexpert Full Dataset", "Chextxray14-NIH_256" and "Chextxray14-NIH_512")


3. Setup your environment variables in the .env file:
  - Example for .env file is provided as ".env.example" in the repository's root folder. 
  - The easiest and most reliable way to set this up is to copy the example file using `cp .env.example .env` and edit only the `GOOGLE_APPLICATION_CREDENTIALS` variable
  - Required variables are:
    - `GOOGLE_APPLICATION_CREDENTIALS`: The path to the service account key json that gives access to the "idp-models" bucket on Google Cloud Storage. This will be required to upload/download models that have been trained and should be added to the pipeline as well as for executing the test pipeline. Additional information on the process for experiment logging can be found in the wiki under [Experiment Logging](https://git.veios.cloud/idp1-radio/idp-radio-1/-/wikis/Experiment%20Logging). To get the service account key either contact [@Kristian.Schwienbacher](https://git.veios.cloud/kristian.schwienbacher) or if you have access to the "idp-server-1" go to the repository's root folder and navigage into `gcp_auth`. There you will also find the service account key. 
    - `CHEXPERT_DATASET_DIRECTORY`: The path to the chexpert data set that should be used in notebooks and scripts. 
    - `CHESTXRAY14_DATASET_DIRECTORY`: The path to the chexpert data set that should be used in notebooks and scripts. 
    - All paths can be absolut or releative to the repository's root folder
    - **Important:** Make sure that the paths you provide for the datasets as environment variables match the directory that they are downloaded to. This should be the case for the `.env.example` file. 
  
 
## Deploy using Docker

1. Clone this repository

2. Install [Docker](https://docs.docker.com/engine/install/ubuntu/)

3. Build the Docker Image: 
```
docker build --tag idp-radio .
```
This will build a docker image based on the tensorflow image, install all dependecies and download the dataset into the container. 

4. Start and run commands in the Docker container: 
```
docker run -d -v $PWD:/srv/idp-radio-1 --name radio --gpus all idp-radio
```
  - `-d` runs the container in detached mode
  - `-v` mounts the src folder into the docker container
  - `--name radio` sets the name of the container to `radio`
  - `--gpus all` enables all GPUs
  - The working directory inside the container is `/srv/idp-radio-1` 


5. To access Jupyter Lab/Notebook or Tensorboard you need to get the public url for the ngrok tunnel. The most comfortable way to get this is to look at the logs of the docker container using:
```
docker logs radio
```

There you will find an entry from where you can copy & paste the public url for the service you'd like to access. The entry is generated every 5 minutes and should look like this: 
```
Retrieving open ngrok tunnels...
URLs open for the following services: [('jupyterlab', 'http://abcde12345.ngrok.io'), ('tensorboard', 'http://abcde12345.ngrok.io'), ('jupyternotebook', 'http://abcde12345.ngrok.io')]
```


6. To start an interative bash to run commands in the container use:
```
docker exec -it radio /bin/bash
```

To run with root access inside the conatiner use:
```
docker exec --user root -it radio  /bin/bash
```


## Run the demo application
### Locally

1. Clone this repository

2. Install the dependencies
```
pip install -r requirements.txt
```

3. Run the application
```
PYTHONPATH=/path/to/repo streamlit run app/main.py
```

4. The application will run on port `8501` of your local machine


### Using docker

1. Clone this repository

2. Install [Docker](https://docs.docker.com/engine/install/ubuntu/)

3. Build the docker image
```
docker build . -f Dockerfile-streamlit --tag idp1-demo
```

4. Start the docker container
```
docker run -v $PWD:/srv/idp-radio-1 --name radio idp1-demo
```

5. The application will run on port `8501` of your local machine
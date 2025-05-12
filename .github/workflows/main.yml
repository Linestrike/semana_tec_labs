name: DockerImage

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:

  prediction:
    runs-on: ubuntu-latest
    container: adsoft/ai-ops:latest

    steps:
    - uses: actions/checkout@v2
    
    - name: run python program 
      run: python linear.py
 
    - name: list files
      run: ls -la  
 
    - name: docker login
      env:
        DOCKER_USER: ${{secrets.DOCKER_USER}}
        DOCKER_PASSWORD: ${{secrets.DOCKER_PASSWORD}}
      run: |
        docker login -u $DOCKER_USER -p $DOCKER_PASSWORD 
        
    - name: Download and run the Docker base image
      run: docker run -d --name serving_base tensorflow/serving
 
    - name: copy model to the Docker image
      run: docker cp ${{secrets.MODEL_NAME}} serving_base:/models/${{secrets.MODEL_NAME}}
      
    - name: Build the custom Docker image
      run: docker commit --change "ENV MODEL_NAME ${{secrets.MODEL_NAME}}" serving_base ${{secrets.DOCKER_USER}}/tensorflow-${{secrets.MODEL_NAME}}:${{ github.sha }}
  
    - name: Docker Push
      run: docker push ${{secrets.DOCKER_USER}}/tensorflow-${{secrets.MODEL_NAME}}:${{ github.sha }}

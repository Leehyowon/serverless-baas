# Serverless BaaS Scenario Tester

The application is written for [KTC Serverless Service](https://serverlessdev-web.ktcloud.com:12581/auth/login). 


## Prerequisite for Flask

1. Directory Architecture

    ```sh
    ㄴ app.py
    ㄴ templates
        ㄴ $FILE_NAME.html
    ㄴ static
        ㄴ $FILE_NAME.css 
            ...
    ㄴ requirements.txt
    ㄴ Dockerfile
    ㄴ test_error.log
    ```

1.  Flask Template Setting

    To reference static files(e.g., css, js) in template files(e.g., html), add the url in the template files according to the form below.

    ```sh
    <link rel="stylesheet" href="{{ url_for('static', filename='$FILE_NAME.css') }}">
    # <link rel="stylesheet" href="$FILE_NAME.css">
    ```

1.  app.py

    Run the Flask server with app.py file.
    Recommend to set the file name 'app.py' to skip setting the environment vaiables.
    Add dictConfig in the app.py file to generate test_error.log file to debug easier.
    Set host '0.0.0.0' to allow external access.

    ```sh
    from flask import Flask, render_template
    import webbrowser
    import os
    from logging.config import dictConfig

    # Debug
    dictConfig({
        'version': 1,
        'formatters': {
            'default': {
                'format': '[%(asctime)s] %(levelname)s in %(module)s: %(message)s',
            }
        },
        'handlers': {
            'file': {
                'level': 'INFO',
                'class': 'logging.handlers.RotatingFileHandler', 
                'filename': 'test_error.log',
                'maxBytes': 1024 * 1024 * 5,  # 5 MB
                'backupCount': 5,
                'formatter': 'default',
            },
        },
        'root': {
            'level': 'INFO',
            'handlers': ['file']
        }
    })

    app = Flask(__name__)

    @app.route("/")
    def $FUNCTION():
        return render_template('$FILE_NAME.html')
        

    if __name__=="__main__":
        app.run(host='0.0.0.0', port='$PORT', debug=True)
    ```


## Build Flask Application using Docker

1.  Dockerfile

    ```sh
    FROM python:3.8             # Base image to use
    COPY . /app                 # Copy the content(.) to '/app' directory in the container
    RUN pip3 install flask      # Run packages, shell cmd, etc. which is necessary to the project
    WORKDIR /app                # Set the workspace
    CMD ["python3", "app.py"]   # Cmd to run
    ```

1.  Requirements.txt

    ```sh
    pip3 freeze > requirements.txt
    ```

1.  Docker Build

    ```sh
    docker build -t $DOCKER_REGISTRY_IP:$DOCKER_REGISTRY_PORT/$REPOGITORY/$IMAGE_NAME:$TAG .
    # docker build -t $REPOSIROTY:$VERSION .
    ```

1.  Docker Run

    ```sh
    docker run -d -p 0.0.0.0:$HOST_PORT:$CONTAINER_PORT --name $CONTAINER_NAME $IMAGE_NAME:$VERSION
    # docker run -d -p 5000:5000 --name test-container test-image:latest
    docker ps       # to verify
    ```

1.  Push the Image to Docker Hub

    ```sh
    docker ps   # to check $CONTAINTER_ID
    docker login
    docker commit $CONTAINER_ID $USER_NAME/$REPOSITORY_NAME:$VERSION
    docker push $USER_NAME/$REPOSITORY_NAME:$VERSION
    ```

1.  Pull the Image from Docker Hub

    ```sh
    docker login
    docker pull $USER_NAME/$REPOSITORY_NAME:$VERSION
    docker image ls    # to check $CONTAINER_IMAGE
    docker run -d -p 0.0.0.0:$HOST_PORT:$CONTAINER_PORT --name $CONTAINER_NAME $REPOSITORY_NAME:$VERSION


## Flask with Micro-service Architecture

1.  Title

    ! spachetti-code

    [reference](https://justkode.kr/python/flask-restapi-2)

    ```sh
    code
    ```
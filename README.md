# gke_docker

### 참고 링크 (1) 구글 클라우드 플랫폼 공식 문서
* https://cloud.google.com/python/django/kubernetes-engine

### 참고 링크 (2) 개인 블로그 시리즈
구글엔진으로 장고 배포하기 - 1. 배포 전 준비
* https://juliahwang.kr/google%20news%20lab/2018/01/29/%EA%B5%AC%EA%B8%80%EC%97%94%EC%A7%84%EC%9C%BC%EB%A1%9C%EC%9E%A5%EA%B3%A0%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B01-%EB%B0%B0%ED%8F%AC%EC%A0%84%EC%A4%80%EB%B9%84.html

구글엔진으로 장고 배포하기 - 2. 프록시 서버 및 데이터베이스 구축
* https://juliahwang.kr/google%20news%20lab/2018/01/29/%EA%B5%AC%EA%B8%80%EC%97%94%EC%A7%84%EC%9C%BC%EB%A1%9C%EC%9E%A5%EA%B3%A0%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B02-%ED%94%84%EB%A1%9D%EC%8B%9C%EC%84%9C%EB%B2%84%EB%B0%8F%EB%94%94%EB%B9%84%EA%B5%AC%EC%B6%95.html

구글엔진으로 장고 배포하기 - 3. Kubernetes 엔진 생성 전 설정
* https://juliahwang.kr/google%20news%20lab/2018/01/29/%EA%B5%AC%EA%B8%80%EC%97%94%EC%A7%84%EC%9C%BC%EB%A1%9C%EC%9E%A5%EA%B3%A0%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B03-Kubernetes%EC%97%94%EC%A7%84%EC%83%9D%EC%84%B1%EC%A0%84%EC%84%A4%EC%A0%95.html

구글엔진으로 장고 배포하기 - 4. 버킷 및 클러스터 엔진 생성
* https://juliahwang.kr/google%20news%20lab/2018/01/29/%EA%B5%AC%EA%B8%80%EC%97%94%EC%A7%84%EC%9C%BC%EB%A1%9C%EC%9E%A5%EA%B3%A0%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B04-%EB%B2%84%ED%82%B7%EC%83%9D%EC%84%B1%EB%B0%8F%EB%8F%84%EC%BB%A4%ED%8C%8C%EC%9D%BC%EB%A7%8C%EB%93%A4%EA%B8%B0.html

구글엔진으로 장고 배포하기 - 5. 도커파일 생성 및 배포 완료
* https://juliahwang.kr/google%20news%20lab/2018/01/29/%EA%B5%AC%EA%B8%80%EC%97%94%EC%A7%84%EC%9C%BC%EB%A1%9C%EC%9E%A5%EA%B3%A0%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B05-%EB%8F%84%EC%BB%A4%EA%B5%AC%EB%8B%88%EC%BD%98%EB%B0%B0%ED%8F%AC%EC%99%84%EB%A3%8C.html

### 1. requirments.txt 준비
      absl-py==0.7.1
      astor==0.7.1
      audioread==2.1.6
      cachetools==3.1.0
      certifi==2019.3.9
      chardet==3.0.4
      decorator==4.4.0
      Django==2.2
      django-extensions==2.1.6
      falcon==1.2.0
      gast==0.2.2
      google-api-core==1.10.0
      google-auth==1.6.3
      google-cloud-core==0.29.1
      google-cloud-storage==1.15.0
      google-resumable-media==0.3.2
      googleapis-common-protos==1.5.10
      grpcio==1.20.1
      gunicorn==19.7.1
      h5py==2.9.0
      idna==2.8
      inflect==0.2.5
      joblib==0.13.2
      Keras-Applications==1.0.7
      Keras-Preprocessing==1.0.9
      librosa==0.5.1
      llvmlite==0.28.0
      Markdown==3.1
      mock==2.0.0
      mysqlclient==1.4.2.post1
      nltk==3.4.1
      numba==0.43.1
      numpy==1.16.3
      pbr==5.2.0
      protobuf==3.7.1
      pyasn1==0.4.5
      pyasn1-modules==0.2.5
      python-mimeparse==1.6.0
      pytz==2019.1
      requests==2.21.0
      resampy==0.2.1
      rsa==4.0
      scikit-learn==0.20.3
      scipy==1.2.1
      six==1.12.0
      sqlparse==0.3.0
      tensorboard==1.13.1
      tensorflow==1.13.1
      tensorflow-estimator==1.13.0
      termcolor==1.1.0
      tqdm==4.11.2
      Unidecode==0.4.20
      urllib3==1.24.3
      Werkzeug==0.15.2
      wincertstore==0.2
      matplotlib
      
### 데이터베이스 설정 구성  

sql 프록시로 내 로컬과 GCP mySQL 연결후

로컬 테스트용 데이터베이스 액세스에 대한 환경 변수를 설정
       
        set DATABASE_USER=내 아이디
        set DATABASE_PASSWORD=내 비밀번호
        
   
이제 내 장고 어플리케이션의 settings.py 에서 데이터베이스를 바꿔 준다.

      DATABASES = {
          'default': {
            'ENGINE': 'django.db.backends.mysql',
            'NAME': 'maratron',
            'USER': os.getenv('DATABASE_USER'),
            'PASSWORD': os.getenv('DATABASE_PASSWORD'),
            'HOST': '127.0.0.1',
            'PORT': '3306',
            }
        }

### Cloud Storage에 버킷 생성

구글 클라우드 플랫폼에서는 Gunicorn 서버를 사용하여 앱을 배포한다. 하지만 Gunicorn 서버는 정적 파일을 서빙하지 않기 때문에 따로 Cloud Storage를 생성하여 정적파일을 서빙해줘야 한다.

먼저 버켓을 생성하고 기본적으로 버켓을 공개해놓는다.


      gsutil mb gs://<버켓이름>
      
      gsutil defacl set public-read gs://<버켓이름> 
      
      python manage.py collectstatic
      
      gsutil rsync -R static/ gs://내장고프로젝트폴더/static

그 다음으로 장고 settings.py에 static url 을 클라우드로 연결

      STATIC_URL="http://storage.googleapis.com/<버켓이름>/static/"
      
      
### Kubernetes 엔진 클러스터 생성하기
GKE를 초기화하려면 GCP 콘솔로 이동합니다. 'Kubernetes Engine을 준비하는 중이며 1분 이상 걸릴 수 있습니다' 메시지가 사라질 때까지 기다립니다.
GKE 클러스터를 만듭니다.

      gcloud container clusters create <클러스터명> \
        --scopes "https://www.googleapis.com/auth/userinfo.email","cloud-platform" \
        --num-nodes 4 --zone "asia-northeast1-a"

클러스터가 만들어지면 gcloud 도구와 통합된 kubectl 명령줄 도구를 사용하여 GKE 클러스터와 상호작용합니다. gcloud와 kubectl은 별도의 도구이므로 kubectl이 올바른 클러스터와 상호작용하도록 구성되었는지 확인합니다.

      gcloud container clusters get-credentials <클러스터명> --zone "asia-northeast1-a"
      
GKE 앱이 Cloud SQL 인스턴스와 연결할 수 있으려면 여러 개의 비밀번호가 필요합니다. 하나는 인스턴스 수준 액세스(연결)를 위해 필요하고 다른 2개는 데이터베이스 액세스용으로 필요합니다. 2가지 수준의 액세스 제어에 대한 자세한 내용은 인스턴스 액세스 제어를 참조.

인스턴스 수준 액세스용으로 비밀번호를 만들려면 서비스 계정을 만들 때 다운로드한 키의 위치를 제공합니다.

    kubectl create secret generic cloudsql-oauth-credentials --from-file=credentials.json=<내파일위치/내파일>.json

    kubectl create secret generic cloudsql --from-literal=username=<DB사용자 명> --from-literal=password=<DB 비밀번호>


Cloud SQL 프록시용 Docker 공개 이미지를 검색합니다.(윈도우에 도커가 깔려 있어야 함)

        docker pull b.gcr.io/cloudsql-docker/gce-proxy:1.05
        
 
Dockerfile 
구성

      FROM gcr.io/google_appengine/python

      RUN virtualenv -p python3 /env
      ENV PATH /env/bin:$PATH

      ADD requirements.txt /app/requirements.txt
      RUN /env/bin/pip install --upgrade pip && /env/bin/pip install -r /app/requirements.txt
      ADD . /app

      CMD gunicorn -b :$PORT ttsProject.wsgi
      
      
 your-project-id를 프로젝트 ID로 바꾸고 Docker 이미지를 만듭니다.
  
        docker build -t gcr.io/<your-project-id>/maratron .
        
        gcloud auth configure-docker
        
   
  Docker 이미지를 푸시합니다. your-project-id를 프로젝트 ID로 바꿉니다.
      
        docker push gcr.io/<your-project-id>/maratron
 
  
  maratron.yaml 
  파일 구성
 
 

          apiVersion: extensions/v1beta1
        kind: Deployment
        metadata:
          name: maratron
          labels:
            app: maratron
        spec:
          replicas: 3
          template:
            metadata:
              labels:
                app: maratron
            spec:
              containers:
              - name: maratron
                image: gcr.io/내프로젝트 아이디/maratron
                imagePullPolicy: Always
                env:
                    - name: DATABASE_USER
                      valueFrom:
                        secretKeyRef:
                          name: cloudsql
                          key: username
                    - name: DATABASE_PASSWORD
                      valueFrom:
                        secretKeyRef:
                          name: cloudsql
                          key: password
                ports:
                - containerPort: 8080

              - image: b.gcr.io/cloudsql-docker/gce-proxy:1.05
                name: cloudsql-proxy
                command: ["/cloud_sql_proxy", "--dir=/cloudsql",
                          "-instances=내프로젝트아이디:sql서비스지역:SQL인스턴스이름=tcp:3306",
                          "-credential_file=/secrets/cloudsql/credentials.json"]
                volumeMounts:
                  - name: cloudsql-oauth-credentials
                    mountPath: /secrets/cloudsql
                    readOnly: true
                  - name: ssl-certs
                    mountPath: /etc/ssl/certs
                  - name: cloudsql
                    mountPath: /cloudsql
              volumes:
                - name: cloudsql-oauth-credentials
                  secret:
                    secretName: cloudsql-oauth-credentials
                - name: ssl-certs
                  hostPath:
                    path: /etc/ssl/certs
                - name: cloudsql
                  emptyDir:

            ---

            apiVersion: v1
            kind: Service
            metadata:
              name: maratron
              labels:
                app: maratron
            spec:
              type: LoadBalancer
              ports:
              - port: 80
                targetPort: 8080
              selector:
                app: maratron



GKE 리소스를 만듭니다.
      
      kubectl create -f maratron.yaml
      
리소스가 만들어진 후 클러스터에 3개의 maratron 포드가 있어야 합니다. 포트의 상태를 확인합니다.
    
      kubectl get pods

포드가 준비되면 부하 분산기의 공개 IP 주소를 가져올 수 있습니다.

      kubectl get services maratron
      
 ![image](https://user-images.githubusercontent.com/29301607/57431038-7535b800-726c-11e9-9bbf-6ad0cb4c2287.png)





# dockerでJenkins構築

1. Jenkins dockerコンテナのダウンロード

    ```bash
    > docker pull jenkinsci/blueocean
    ```

1. Jenkins起動

    永続化用のフォルダ作成

    ```bash
    > pwd
    /Users/okawauchi/Desktop/workspace

    > mkdir jenkins-data
    ```

    ```bash
    > docker run -d -v /Users/okawauchi/Desktop/workspace/jenkins-data:/var/jenkins_home -p 8080:8080 -p 50000:50000 jenkins/jenkins:lts
    c88e5135151675e1ecd3b9c438a2dad85d6b509555d54a01e3e68e59d691a798
    ```

1. Jenkins停止

    コンテナ起動確認

    ```bash
    $ docker ps -a
    CONTAINER ID        IMAGE                                            COMMAND                  CREATED             STATUS                        PORTS                                              NAMES
    23920c6a0fae        jenkins/jenkins:lts                              "/sbin/tini -- /usr/…"   4 minutes ago       Up 4 minutes                  0.0.0.0:8080->8080/tcp, 0.0.0.0:50000->50000/tcp   crazy_colden
    be14450a44fe        jenkins/jenkins:lts                              "/sbin/tini -- /usr/…"   11 minutes ago      Exited (143) 7 minutes ago                                                       boring_pike
    c88e51351516        jenkins/jenkins:lts                              "/sbin/tini -- /usr/…"   2 hours ago         Exited (143) 15 minutes ago                                                      infallible_bose
    7d4f6a638ec7        docker.bintray.io/jfrog/artifactory-oss:latest   "/entrypoint-artifac…"   6 days ago          Exited (143) 3 days ago                                                          artifactory
    5d74d2ad333c        mysql:latest                                     "docker-entrypoint.s…"   3 months ago        Up 3 hours                    0.0.0.0:3306->3306/tcp, 33060/tcp                  mysql
    ```

    コンテナ停止

    ```bash
    $ docker stop 23920c6a0fae
    23920c6a0fae
    ```

    コンテナ起動確認

    ```bash
    $ docker ps -a
    CONTAINER ID        IMAGE                                            COMMAND                  CREATED             STATUS                            PORTS                               NAMES
    23920c6a0fae        jenkins/jenkins:lts                              "/sbin/tini -- /usr/…"   7 minutes ago       Exited (143) About a minute ago                                       crazy_colden
    be14450a44fe        jenkins/jenkins:lts                              "/sbin/tini -- /usr/…"   14 minutes ago      Exited (143) 10 minutes ago                                           boring_pike
    c88e51351516        jenkins/jenkins:lts                              "/sbin/tini -- /usr/…"   2 hours ago         Exited (143) 17 minutes ago                                           infallible_bose
    7d4f6a638ec7        docker.bintray.io/jfrog/artifactory-oss:latest   "/entrypoint-artifac…"   6 days ago          Exited (143) 3 days ago                                               artifactory
    5d74d2ad333c        mysql:latest                                     "docker-entrypoint.s…"   3 months ago        Up 3 hours                        0.0.0.0:3306->3306/tcp, 33060/tcp   mysql
    ```

    コンテナ削除

    ```bash
    $ docker rm 23920c6a0fae be14450a44fe c88e51351516
    23920c6a0fae
    be14450a44fe
    c88e51351516
    ```

    コンテナ確認

    ```bash
    $ docker ps -a
    CONTAINER ID        IMAGE                                            COMMAND                  CREATED             STATUS                    PORTS                               NAMES
    7d4f6a638ec7        docker.bintray.io/jfrog/artifactory-oss:latest   "/entrypoint-artifac…"   6 days ago          Exited (143) 3 days ago                                       artifactory
    5d74d2ad333c        mysql:latest                                     "docker-entrypoint.s…"   3 months ago        Up 3 hours                0.0.0.0:3306->3306/tcp, 33060/tcp   mysql
    ```

    コンテナイメージ確認

    ```bash
    $ docker images
    REPOSITORY                                TAG                 IMAGE ID            CREATED             SIZE
    docker.bintray.io/jfrog/artifactory-oss   latest              0676af64282c        10 days ago         804MB
    jenkins/jenkins                           lts                 fac78e370c0b        12 days ago         568MB
    mysql                                     latest              de764ad211de        3 months ago        443MB
    ```

    コンテナ作成

    ```bash
    $ docker run -d -v /Users/okawauchi/Desktop/workspace/jenkins-data:/var/jenkins_home -p 8080:8080 -p 50000:50000 --name jenkins-docker jenkins/jenkins:lts  
    7027d2407627de7b3006f434c36d7efed0e4078595d737faac6b54aa4e584d0b
    ```

    コンテナ起動確認

    ```bash
    $ docker ps -a
    CONTAINER ID        IMAGE                                            COMMAND                  CREATED             STATUS                    PORTS                                              NAMES
    7027d2407627        jenkins/jenkins:lts                              "/sbin/tini -- /usr/…"   3 seconds ago       Up 2 seconds              0.0.0.0:8080->8080/tcp, 0.0.0.0:50000->50000/tcp   jenkins-docker
    7d4f6a638ec7        docker.bintray.io/jfrog/artifactory-oss:latest   "/entrypoint-artifac…"   6 days ago          Exited (143) 3 days ago                                                      artifactory
    5d74d2ad333c        mysql:latest                                     "docker-entrypoint.s…"   3 months ago        Up 3 hours                0.0.0.0:3306->3306/tcp, 33060/tcp                  mysql
    ```

    コンテナ停止

    ```bash
    $ docker stop jenkins-docker
    jenkins-docker
    ```

    `jenkins-docker`は停止していることを確認

    ```bash
    $ docker ps -a
    CONTAINER ID        IMAGE                                            COMMAND                  CREATED             STATUS                       PORTS                               NAMES
    7027d2407627        jenkins/jenkins:lts                              "/sbin/tini -- /usr/…"   4 minutes ago       Exited (143) 3 minutes ago                                       jenkins-docker
    7d4f6a638ec7        docker.bintray.io/jfrog/artifactory-oss:latest   "/entrypoint-artifac…"   6 days ago          Exited (143) 3 days ago                                          artifactory
    5d74d2ad333c        mysql:latest                                     "docker-entrypoint.s…"   3 months ago        Up 3 hours                   0.0.0.0:3306->3306/tcp, 33060/tcp   mysql
    ````

1. Jenkinsプラグインで発生した事象に対する処置

    Jenkinsの`HTML publisher`プラグインでHTMLを公開すると、以下のエラーがブラウザで発生する。

    ```bash
    Blocked script execution in '<URL>' because the document's frame is sandboxed and the 'allow-scripts' permission is not set.
    ```

    回避策はdocker Jenkins起動時に以下を設定する。

    ```bash
    docker run -d -v /Users/okawauchi/Desktop/workspace/jenkins-data:/var/jenkins_home -p 8080:8080 --env JENKINS_JAVA_OPTIONS="-Djava.awt.headless=true -Dhudson.model.DirectoryBrowserSupport.CSP=\"default-src 'self' 'unsafe-inline' 'unsafe-eval'; img-src data:;\"" --name jenkins jenkinsci/blueocean
    ```

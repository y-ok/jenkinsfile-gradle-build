# dockerでJenkins構築

1. Jenkins dockerコンテナのダウンロード

    ```bash
    $ docker pull jenkins/jenkins:lts
    lts: Pulling from jenkins/jenkins
    9a0b0ce99936: Pull complete
    db3b6004c61a: Pull complete
    f8f075920295: Pull complete
    6ef14aff1139: Pull complete
    962785d3b7f9: Pull complete
    631589572f9b: Pull complete
    c55a0c6f4c7b: Pull complete
    911269a281e7: Pull complete
    45099f90ad07: Pull complete
    bd655133fb06: Pull complete
    ea116f49f692: Pull complete
    a9e13a874162: Pull complete
    bdbca454a0c4: Pull complete
    9b2fec49ceaa: Pull complete
    765aaab2ff5b: Pull complete
    a46bfc77dc0f: Pull complete
    2cd8e1655f4a: Pull complete
    4d138a7aea1a: Pull complete
    Digest: sha256:c3b6fa9216336022067be94adb9d4696459903bf3e67e01a8d0009994f5ab78d
    Status: Downloaded newer image for jenkins/jenkins:lts
    docker.io/jenkins/jenkins:lts
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
    docker run -d -v /Users/okawauchi/Desktop/workspace/jenkins-data:/var/jenkins_home -p 8080:8080 -p 50000:50000 --env JAVA_OPTS="-Dhudson.model.DirectoryBrowserSupport.CSP=\"\"" --name jenkins jenkins/jenkins:lts
    ```

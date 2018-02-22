1. create a custom version of flink that includes some useful debug tooling

       docker build -t ecg/flink_1.4.0-prometheus-hadoop28-scala_2.11 docker

2. Bring up the job manager and task manager

       docker-compose up -d

4. View the taskmanager console output 

       docker-compose logs -f -t taskmanager

5. Temporarily remove connectivity between the jobmanager and the taskmanager

       docker exec -t jobmanager iptables -A INPUT -i eth0 -p tcp --destination-port 6123 --src taskmanager -j DROP

6. Wait until the taskmanager at http://localhost:8081/#/taskmanagers disapears. Then restore connectivity with

       docker exec -t jobmanager iptables -X && docker exec -t jobmanager iptables -F  


Now check the task manager log you started tailing in step 4 and you'll notice entries like


    taskmanager    | 2018-02-22 17:50:52,014 INFO  akka.remote.Remoting                                          - Quarantined address [akka.tcp://flink@jobmanager:6123] is still unreachable or has not been restarted. Keeping it quarantined.
    taskmanager    | 2018-02-22 17:51:22,023 INFO  org.apache.flink.runtime.taskmanager.TaskManager              - Trying to register at JobManager akka.tcp://flink@jobmanager:6123/user/jobmanager (attempt 17, timeout: 30000 milliseconds)
    taskmanager    | 2018-02-22 17:51:22,038 INFO  akka.remote.Remoting                                          - Quarantined address [akka.tcp://flink@jobmanager:6123] is still unreachable or has not been restarted. Keeping it quarantined.

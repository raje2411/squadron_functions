## squadron_functions -  You can copy paste this code into your MAC ~/.bash_profile file ,  modify the password next to the keyword "sshpass -p "

###Shell function to login to squadron machines.

c(){
sshpass -p rraman ssh -o "StrictHostKeyChecking no" root@c"$1"-node"$2" "$3"
}
export -f c


###Shell function to start all services in a Squadron cluster

start_all(){
curl -i -u admin:rraman -H "X-Requested-By: ambari"  -X PUT  -d '{"RequestInfo":{"context":"_PARSE_.START.ALL_SERVICES","operation_level":{"level":"CLUSTER","cluster_name":"$1"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}' http://$1-node1:8080/api/v1/clusters/$1/services
}
export -f start_all

###Shell function to stop all services in a Squadron cluster

stop_all(){
curl -i -u admin:rraman -H "X-Requested-By: ambari"  -X PUT  -d '{"RequestInfo":{"context":"_PARSE_.STOP.ALL_SERVICES","operation_level":{"level":"CLUSTER","cluster_name":"$1"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}' http://$1-node1:8080/api/v1/clusters/$1/services
}
export -f stop_all

###Function to automate creating aliases 

create_alias(){
echo "alias $1='cd `pwd`'" >> ~/.bash_profile
source ~/.bash_profile
}
export -f create_alias

###To backup - mysql databases running on a specific node  - some more optimization in progress

backup_mysql(){
c 349 1 "mysqldump --all-databases > /root/mysql_all_dbs_backup_dump_\`date "+%Y-%m-%d"`\.sql"
c 349 1 "mysql -u root -e 'show databases' | grep -v 'Database' |egrep -i 'ranger|hive|ambari|mysql' | while read dbname;  do mysqldump $"dbname" > /root/mysql_db_\$"dbname"\_backup_\`date "+%Y-%m-%d"`\.sql; done"
}
export -f backup_mysql

###This function will create keytabs() function in all the squadron cluster machines

###usage:    create_keytabs "c 149"

create_keytabs(){
for i in {1..5};do $1 $i "echo  `cat /Users/rraman/.ssh/keytabs_func_code.txt` >> ~/.bash_profile";done
}
export -f create_keytabs

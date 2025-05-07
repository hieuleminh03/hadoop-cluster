# Hadoop Cluster with HA
### Version:
* Hadoop 3.3.1 (including YARN)
* Open JDK 8
* Hive 2.3.9
* Zookeeper 3.6.3

 <table>
  <tr>
    <td>Web UI</td>
    <td>Url</td>
  </tr>
  <tr>
    <td>NameNode</td>
    <td>http://localhost:9870  http://localhost:9870</td>
  </tr>
  <tr>
    <td>ResourceManager</td>
    <td>http://localhost:8088 http://localhost:8089</td>
  </tr><tr>
    <td>HistoryServer</td>
    <td>http://localhost:19888</td>
  </tr><tr>
    <td>JournalNode</td>
    <td>http://localhost:8480 http://localhost:8481 http://localhost:8482</td>
  </tr>
</table>

<br/><br/>
#### The sequence of setting up Hadoop Cluster:
1. source CreateImage.sh: Creates the needed Docker images.
2. docker-compose up -d: Builds these Hadoop containers.
3. source Startup.sh: Initializes and starts the Hadoop Cluster components.
 
<br/><br/>
**Project Focus:**
1. High Availability (HA) and Failover for NameNode and ResourceManager
2. SSH connectivity between containers without passwords
3. Using Docker exec for sending commands to containers
4. Configuration management for distributed systems
5. Management of network ports in Docker environments
6. Docker installation via snap install docker

![](https://getkong.org/assets/images/homepage/intro-illustration.png)


![](/assets/331F4F42-03C3-4D19-ACDC-3204862151F7.png)
**Cent OS** **Installation:**

1. **Install the Package:**

  **If you are downloading the **[**package**](https://getkong.org/install/centos/#packages)**, execute:**

  ```
  $ sudo yum install epel-release
  $ sudo yum install kong-0.9.1.*.noarch.rpm --nogpgcheck

  ```

2. **Configure your database**

  [**Configure**](https://getkong.org/docs/0.9.x/configuration#database)** Kong so it can connect to your database. Kong supports both **[**PostgreSQL 9.4+**](http://www.postgresql.org/)** and**[**Cassandra 2.2.x**](http://cassandra.apache.org/)** as its datastore.**

  **If you are using Postgres, please provision a database and a user before starting Kong, ie:**

  ```
  CREATE USER kong; CREATE DATABASE kong OWNER kong;

  ```

3. **Start Kong:**

  ```
  $ kong start

  # Kong is running
  $ curl 127.0.0.1:8001

  ```

4. **Use Kong:**

  **Quickly learn how to use Kong with the **[**5-minute Quickstart**](https://getkong.org/docs/latest/getting-started/quickstart)**.**


![](/assets/A18E5EA3-1965-4B5F-ADA7-5C5E18495E19.png)


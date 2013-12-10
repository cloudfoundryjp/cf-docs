---
title: ユーザによるサービスのインスタンス
---

ユーザによるサービスのインスタンスとは、Cloud Foundryの外部で動作しているサービスのインスタンスです。たとえば、DBAはCloud Foundryに管理されていないOracleデータベースの利用者情報を提供することがあります。こういったインスタンスの利用者情報をソースに直接書くのではなく、Cloud Foundryのモック・サービスを作って外部のサービスへ接続することができます。そのためには慣れ親しんだ`create-service`コマンドが使え、利用者情報もいつものように扱えます。ユーザによるサービスのインスタンスをいったん作れば、通常のサービスのインスタンスと同様に扱えます。

ユーザによるサービスのインスタンスを作る時、Cloud Foundryはその名前と利用者情報の入力を求めます。利用者情報はカンマで区切られたリストです。各パラメータの値の入力が求められます。

<pre class="terminal">
$ cf create-service user-provided
Name?> mydb

What credential parameters should applications use to connect to this service instance?
(e.g. hostname, port, password)> hostname, port, username, password, name     

hostname> db.example.com

port> 1234

username> dbuser

password> dbpasswd

name> mydb

Creating service mydb... OK
</pre>

<pre class="terminal">
$ cf services
Getting services in test... OK

name               service         provider     version   plan        bound apps     
cleardb-28472      cleardb         cleardb      n/a       spark       none           
blazemeter-78fb4   blazemeter      blazemeter   n/a       free-tier   none           
mydb               user-provided   n/a          n/a       n/a         none
</pre> 

ユーザによるサービスのインスタンスの[バインド](managing-services#bind)とアプリの再起動の後、[VCAP_SERVICES](../deploying-apps/environment-variable.html)に利用者情報が追加されているのが確認できます。

~~~
{
  user-provided: [
    {
      name: "mydb",
      label: "user-provided",
      tags: [ ],
      credentials: {
        hostname: "db.example.com",
        port: "1234",
        username: "dbuser",
        password: "dbpasswd",
        name: "mydb"
      }
    }
  ]
}
~~~

ユーザによるインスタンスはプッシュする時に作成とバインドが実行され、マニフェストに保存されます。あらかじめマニフェストに記述しておけば、次にプッシュする時にインスタンスが作成されバインドされます; スペース内に既にインスタンスが存在していれば、そのインスタンスがバインドされます。

<pre class="terminal">
$ cf push spring-music --path build/libs/spring-music.war

...skipping ahead...

Create services for application?> y

1: blazemeter n/a, via blazemeter
2: cleardb n/a, via cleardb
3: cloudamqp n/a, via cloudamqp
4: elephantsql n/a, via elephantsql
5: mongolab n/a, via mongolab
6: newrelic n/a, via newrelic
7: rediscloud n/a, via garantiadata
8: sendgrid n/a, via sendgrid
9: treasuredata n/a, via treasuredata
10: user-provided , via 
What kind?> 10

Name?> user-provided-b702e

What credential parameters should applications use to connect to this service instance?
(e.g. hostname, port, password)> uri

uri> postgres://dbuser:dbpass@db.example.com:1234/dbname

Creating service user-provided-b702e... OK
Binding user-provided-b702e to spring-music... OK
Create another service?> n

Bind other services to application?> n

Save configuration?> y

Saving to manifest.yml... OK

...skipping ahead...

Push successful! App 'spring-music' available at http://spring-music.cfapps.io
</pre>

<pre class="terminal">
$ cat manifest.yml
---
applications:
- name: spring-music
  memory: 512M
  instances: 1
  host: spring-music
  domain: cfapps.io
  path: build/libs/spring-music.war
  services:
    user-provided-b702e:
      label: user-provided
      credentials:
        uri: postgres://dbuser:dbpass@db.example.com:1234/dbname

</pre>


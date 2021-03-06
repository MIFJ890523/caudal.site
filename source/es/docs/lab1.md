title: Lab 1 - Instalación para ambiente de desarrollo
---
Una guía usada para iniciar proyectos nuevo de Caudal.

## Requerimientos
 * [Java 1.8 (OpenJDK or OracleJDK)](java.html)
 * [Leiningen 2.6.1](leiningen.html)

## Construyendo
1. Crea un proyecto nuevo llamado **caudal-labs** desde **caudal-template**
```txt
$ lein new caudal-template caudal-labs
Generating fresh 'lein new' caudal-template project.
```

2. Cambia el directorio actual al del proyecto creado recientemente
```txt
$ cd caudal-labs/
```

3. Transfiere todas las dependencias al directorio **lib**
```txt
$ lein libdir
Copied 143 file(s) to: /projects/caudal-labs/lib
```

4. Construye el nuevo proyecto **caudal-labs**
```txt
$ lein jar
Warning: specified :main without including it in :aot.
Implicit AOT of :main will be removed in Leiningen 3.0.0.
If you only need AOT for your uberjar, consider adding :aot :all into your
:uberjar profile instead.
Compiling caudal-labs.custom
Warning: The Main-Class specified does not exist within the jar. It may not be executable as expected. A gen-class directive may be missing in the namespace which contains the main method.
Created /projects/caudal-labs/target/caudal-labs-0.1.2-SNAPSHOT.jar
```

5. Copia el jar del proyecto en el directorio **lib**
```txt
$ cp target/caudal-labs-0.1.2-SNAPSHOT.jar lib/
```

## Arrancando

1. Inicia el proyecto Caudal usando la configuración por defecto desde el archivo **./config/caudal-config.clj**
```txt
$ ./bin/start-caudal.sh -c ./config/caudal-config.clj
Verifying JAVA instalation ...
/usr/bin/java
JAVA executable found in PATH
JAVA Version : 1.8.0_91
BIN path /projects/caudal-labs/bin
Starting Caudal from /projects/caudal-labs
                        __      __
  _________ ___  ______/ /___ _/ /
 / ___/ __ `/ / / / __  / __ `/ /
/ /__/ /_/ / /_/ / /_/ / /_/ / /
\___/\__,_/\__,_/\__,_/\__,_/_/

Caudal 0.7.4-SNAPSHOT
log4j:WARN [stdout] should be System.out or System.err.
log4j:WARN Using previously set target, System.out by default.
log4j:WARN [stdout] should be System.out or System.err.
log4j:WARN Using previously set target, System.out by default.
SLF4J: Class path contains multiple SLF4J bindings.
SLF4J: Found binding in [jar:file:/projects/caudal-labs/lib/logback-classic-1.1.3.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding in [jar:file:/projects/caudal-labs/lib/slf4j-log4j12-1.7.5.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
SLF4J: Actual binding is of type [ch.qos.logback.classic.util.ContextSelectorStaticBinder]
15:15:08.327 [main] INFO  mx.interware.caudal.core.starter-dsl - {:loading-dsl {:file ./config/caudal-config.clj}}
15:15:09.711 [main] INFO  mx.interware.caudal.io.tcp-server - Starting server on port :  9900  ...
15:15:09.767 [main] INFO  mx.interware.caudal.io.tailer-server - Tailing files :  (/projects/caudal-labs/./data/input.txt)  ...
15:15:09.775 [main] INFO  mx.interware.caudal.io.tailer-server - Channel created for file  input.txt  - > channel :  #object[clojure.core.async.impl.channels.ManyToManyChannel 0x8d7b252 clojure.core.async.impl.channels.ManyToManyChannel@8d7b252]
15:15:09.855 [main] INFO  mx.interware.caudal.io.tailer-server - register-channels for tailer
15:15:10.064 INFO  [org.quartz.core.QuartzScheduler] (main) Quartz Scheduler v.2.2.1 created.
15:15:10.068 INFO  [org.quartz.core.QuartzScheduler] (main) Scheduler meta-data: Quartz Scheduler (v2.2.1) 'SimpleQuartzScheduler:-1011881444' with instanceId 'SIMPLE_NON_CLUSTERED:-1011881444'
  Scheduler class: 'org.quartz.core.QuartzScheduler' - running locally.
  NOT STARTED.
  Currently in standby mode.
  Number of jobs executed: 0
  Using thread pool 'org.quartz.simpl.SimpleThreadPool' - with 5 threads.
  Using job-store 'org.quartz.simpl.RAMJobStore' - which does not support persistence. and is not clustered.

15:15:10.069 INFO  [org.quartz.core.QuartzScheduler] (main) Scheduler SimpleQuartzScheduler:-1011881444_$_SIMPLE_NON_CLUSTERED:-1011881444 started.
15:15:10.069 INFO  [org.projectodd.wunderboss.scheduling.Scheduling] (main) Quartz started
```

## Probando
1. Abre otra terminal y envía un evento de prueba usando el **tailer** de Caudal, agregando el mensaje al archivo **data/input.txt**
```txt
$ echo "{:host \"server1\" :service \"http\" :customer \"orinoco\" :id 42 :cost 14.2857}" > data/input.txt
```

2. Verifica la bitácora generada por el evento entrante
```txt
line :  {:host "server1" :service "http" :customer "orinoco" :id 42 :cost 14.2857}
{:event-1 {:host server1, :service http, :customer orinoco, :id 42, :cost 14.2857, :caudal/latency 713696}}
```

3. Envía otro evento usando el canal **tcp** de Caudal corriendo en el puerto **9900**
```txt
$ telnet localhost 9900
Trying ::1...
Connected to localhost.
Escape character is '^]'.
{:host "server2" :service "ssl" :customer "amazonas" :id 101 :cost 28.5714}
```

4. Verifica la bitácora generada para el evento recibido
```txt
15:19:19.832 INFO  [org.apache.mina.filter.logging.LoggingFilter] (NioProcessor-2) CREATED
15:19:19.832 INFO  [org.apache.mina.filter.logging.LoggingFilter] (NioProcessor-2) OPENED
15:19:21.109 INFO  [org.apache.mina.filter.logging.LoggingFilter] (NioProcessor-2) RECEIVED: HeapBuffer[pos=0 lim=77 cap=4096: 7B 3A 68 6F 73 74 20 22 73 65 72 76 65 72 32 22...]
{:event-1 {:host server2, :service ssl, :customer amazonas, :id 101, :cost 28.5714, :caudal/latency 268423}}
MAIN - Customer    : {:host "server2", :service "ssl", :customer "amazonas", :id 101, :cost 28.5714, :caudal/latency 555964, :path "A good one"}
MAIN - Adjusted *  : {:host "server2", :service "ssl", :customer "amazonas", :id 101, :cost 33.142824, :caudal/latency 555964, :path "A good one", :count 1}
MAIN - Adjusted +  : {:host "server2", :service "ssl", :customer "amazonas", :id 101, :cost 133.142824, :caudal/latency 555964, :path "A good one", :count 1}
MAIN - Timestamped : {:host "server2", :service "ssl", :customer "amazonas", :id 101, :cost 28.5714, :caudal/latency 555964, :path "A good one", :time 1485206361129}
```

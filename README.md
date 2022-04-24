### Escuela Colombiana de Ingeniería
### Arquitecturas de Software - ARSW

## Escalamiento en Azure con Maquinas Virtuales, Sacale Sets y Service Plans

### Dependencias
* Cree una cuenta gratuita dentro de Azure. Para hacerlo puede guiarse de esta [documentación](https://azure.microsoft.com/es-es/free/students/). Al hacerlo usted contará con $100 USD para gastar durante 12 meses.

### Parte 0 - Entendiendo el escenario de calidad

Adjunto a este laboratorio usted podrá encontrar una aplicación totalmente desarrollada que tiene como objetivo calcular el enésimo valor de la secuencia de Fibonnaci.

**Escalabilidad**
Cuando un conjunto de usuarios consulta un enésimo número (superior a 1000000) de la secuencia de Fibonacci de forma concurrente y el sistema se encuentra bajo condiciones normales de operación, todas las peticiones deben ser respondidas y el consumo de CPU del sistema no puede superar el 70%.

### Antes de Crear la máquina virtual
Al revisar las especificaciones, nos piden en la parte de SSh Public Key que tengamos nuestra propia llave pública. Para esto nos dirigimos a la terminal Bash de Azure, esto debido a que el sistema operativo de nuestra máquina virtual será Linux.
![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part1/Primero.png)

Gracias a la documentación proporcionada en el repositorio, creamos un par de llaves SSH con cifrado RSA con longitud en bits de 4096
![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part1/Segundo.png)

La imágen a continuación es la llave generada la cual está guardada en una carpeta local.
![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part1/Consulta.png)

### Parte 1 - Escalabilidad vertical

1. Diríjase a el [Portal de Azure](https://portal.azure.com/) y a continuación cree una maquina virtual con las características básicas descritas en la imágen 1 y que corresponden a las siguientes:
    * Resource Group = SCALABILITY_LAB
    * Virtual machine name = VERTICAL-SCALABILITY
    * Image = Ubuntu Server 
    * Size = Standard B1ls
    * Username = scalability_lab
    * SSH publi key = Su llave ssh publica

![Imágen 1](images/part1/part1-vm-basic-config.png)

Teniendo en cuenta el punto antes de empezar con la creación de la máquina virtual (Creación de llaves SSH), en la clave de cuenta de usuario, se pone la clave pública SSH que se creó.

![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part1/Llave.png)

2. Para conectarse a la VM use el siguiente comando, donde las `x` las debe remplazar por la IP de su propia VM (Revise la sección "Connect" de la virtual machine creada para tener una guía más detallada).

    `ssh scalability_lab@xxx.xxx.xxx.xxx`

![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part1/Conexion.png)

3. Instale node, para ello siga la sección *Installing Node.js and npm using NVM* que encontrará en este [enlace](https://linuxize.com/post/how-to-install-node-js-on-ubuntu-18.04/).

![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part1/Node.png)

![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part1/Version.png)

4. Para instalar la aplicación adjunta al Laboratorio, suba la carpeta `FibonacciApp` a un repositorio al cual tenga acceso y ejecute estos comandos dentro de la VM:

    `git clone <your_repo>`

    `cd <your_repo>/FibonacciApp`

    `npm install`
    
![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part1/Clonar.png)
    
![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part1/npm.png)

5. Para ejecutar la aplicación puede usar el comando `npm FibinacciApp.js`, sin embargo una vez pierda la conexión ssh la aplicación dejará de funcionar. Para evitar ese compartamiento usaremos *forever*. Ejecute los siguientes comando dentro de la VM.

    ` node FibonacciApp.js`
    
![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part1/Quinto.png)

6. Antes de verificar si el endpoint funciona, en Azure vaya a la sección de *Networking* y cree una *Inbound port rule* tal como se muestra en la imágen. Para verificar que la aplicación funciona, use un browser y user el endpoint `http://xxx.xxx.xxx.xxx:3000/fibonacci/6`. La respuesta debe ser `The answer is 8`.

![](images/part1/part1-vm-3000InboudRule.png)

![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part1/Fibo6.png)

7. La función que calcula en enésimo número de la secuencia de Fibonacci está muy mal construido y consume bastante CPU para obtener la respuesta. Usando la consola del Browser documente los tiempos de respuesta para dicho endpoint usando los siguintes valores:
    * 1000000
    
    ![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part1/7-1.png)
    
    * 1010000
    
    ![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part1/7-2.png)
   
    * 1020000
    
    ![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part1/7-3.png)
    
    * 1030000
    
    ![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part1/7-4.png)
    
    * 1040000
    
    ![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part1/7-5.png)
    
    * 1050000
    
    ![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part1/7-6.png)
    
    * 1060000
    
    ![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part1/7-7.png)
    
    * 1070000
    
    ![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part1/7-8.png)
    
    * 1080000
    
    ![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part1/7-9.png)
    
    * 1090000    
    
    ![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part1/7-0.png)

8. Dírijase ahora a Azure y verifique el consumo de CPU para la VM. (Los resultados pueden tardar 5 minutos en aparecer).

 ![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part1/Resultado1.png)

![Imágen 2](images/part1/part1-vm-cpu.png)

9. Ahora usaremos Postman para simular una carga concurrente a nuestro sistema. Siga estos pasos.
    * Instale newman con el comando `npm install newman -g`. Para conocer más de Newman consulte el siguiente [enlace](https://learning.getpostman.com/docs/postman/collection-runs/command-line-integration-with-newman/).
    * Diríjase hasta la ruta `FibonacciApp/postman` en una maquina diferente a la VM.
    * Para el archivo `[ARSW_LOAD-BALANCING_AZURE].postman_environment.json` cambie el valor del parámetro `VM1` para que coincida con la IP de su VM.

     ![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part1/Cambio.png)
    
    * Ejecute el siguiente comando.

    ```
    newman run ARSW_LOAD-BALANCING_AZURE.postman_collection.json -e [ARSW_LOAD-BALANCING_AZURE].postman_environment.json -n 10 &
    newman run ARSW_LOAD-BALANCING_AZURE.postman_collection.json -e [ARSW_LOAD-BALANCING_AZURE].postman_environment.json -n 10
    ```
    
    ![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part1/Notificacion1.png)
    
    ![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part1/1-3.png)
    
    ![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part1/4-7.png)
    
    ![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part1/8-10.png)
    
    ![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part1/Estadisticas1.png)

10. La cantidad de CPU consumida es bastante grande y un conjunto considerable de peticiones concurrentes pueden hacer fallar nuestro servicio. Para solucionarlo usaremos una estrategia de Escalamiento Vertical. En Azure diríjase a la sección *size* y a continuación seleccione el tamaño `B2ms`.

![Imágen 3](images/part1/part1-vm-resize.png)

11. Una vez el cambio se vea reflejado, repita el paso 7, 8 y 9.    

   #### Paso 7 con nuevo tamaño
   * 1000000
    
   ![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part1/7-1-2.png)
    
   * 1010000
    
   ![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part1/7-2-2.png)
   
   * 1020000
    
   ![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part1/7-3-2.png)
    
   * 1030000
    
   ![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part1/7-4-2.png)
    
   * 1040000
    
   ![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part1/7-5-2.png)
    
   * 1050000
    
   ![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part1/7-6-2.png)
    
   * 1060000
    
   ![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part1/7-7-2.png)
    
   * 1070000
    
   ![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part1/7-8-2.png)
    
   * 1080000
    
   ![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part1/7-9-2.png)
    
   * 1090000    
    
   ![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part1/7-0-2.png)
   
   
   #### Paso 8 con nuevo tamaño 
   
   ![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part1/Estadisticas2.png)
   
   
   ### Paso 9 con nuevo tamaño
   
   ![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part1/1-3-2.png)
    
   ![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part1/4-6.png)
    
   ![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part1/7-10.png)
    
   ![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part1/Result2.png)
   
   
12. Evalue el escenario de calidad asociado al requerimiento no funcional de escalabilidad y concluya si usando este modelo de escalabilidad logramos cumplirlo.
13. Vuelva a dejar la VM en el tamaño inicial para evitar cobros adicionales.

**Preguntas**

1. ¿Cuántos y cuáles recursos crea Azure junto con la VM?

 ![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part1/Recursos.png)

2. ¿Brevemente describa para qué sirve cada recurso?

   - Virtual network:
Este tipo de red permite que diferentes recursos de Azure como lo son las máquinas virtuales, se puedan comunicarse de forma segura entre usuarios, con Internet y con las redes locales.

   - Public IP address:
Dirección IP cuyo conjunto de números que identifica, de manera lógica y jerárquica, a una interfaz en la red de un dispositivo que utilice el protocolo o que corresponde al nivel de red del modelo TCP/IP, de tal manera que sea visible con internet y algunos servicios públicos de azure.

   - Network security group:
Se utiliza para filtrar el trafico de red desde y para los recursos de Azure en una red virtual de Azure. Un grupo de seguridad se basa principalmente en un conjunto de reglas de seguridad que permiten o niegan el trafico de red entrante o el trafico de red saliente.

   - Network interface:
Componente que permite que las VM de Azure se comunique con Internet y demas recursos locales.

   - OS disk:
Almacenamiento del OS de la maquina virtual creada.


3. ¿Al cerrar la conexión ssh con la VM, por qué se cae la aplicación que ejecutamos con el comando `npm FibonacciApp.js`? ¿Por qué debemos crear un *Inbound port rule* antes de acceder al servicio?

   - Cuando se inicia la conexion ssh con la VM, se inicia un proceso para este este servicio y este proceso quedara asociado al usuario el cual realizo la conexion, por lo que al cerrar la conexion este proceso se terminara.

4. Adjunte tabla de tiempos e interprete por qué la función tarda tando tiempo.

| N             | Standard_B1ls (s)    | Standars_B2ms (s) |
|---------------|----------------------|-------------------|
| 1000000       | 19.85 s              | 19.61 s           |
| 1010000       | 20.20 s              | 19.79 s           |
| 1020000       | 20.64 s              | 21.10 s           |
| 1030000       | 21.59 s              | 21.64 s           |
| 1040000       | 21.85 s              | 22.59 s           |
| 1050000       | 21.97 s              | 22.72 s           |
| 1060000       | 22.34 s              | 22.87 s           |
| 1070000       | 22.61 s              | 23.39 s           |
| 1080000       | 23.27 s              | 24.10 s           | 
| 1090000       | 23.66 s              | 24.29 s           |

La función tarda demasiado tiempo debido a que la implementación de Fibonacci en código no es la óptima.

5. Adjunte imágen del consumo de CPU de la VM e interprete por qué la función consume esa cantidad de CPU.

   - Consumo de CPU con B1ls

   ![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part1/Resultado1.png)

   - Consumo de CPU con B2ms

   ![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part1/Estadisticas2.png)
   
7. Adjunte la imagen del resumen de la ejecución de Postman. Interprete:
    * Tiempos de ejecución de cada petición.

   - Resultados sin cambio de tamaño
   
  ![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part1/Estadisticas1.png)

   - Resultados con cambio de tamaño

   ![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part1/Result2.png)
  
    * Si hubo fallos documentelos y explique.


8. ¿Cuál es la diferencia entre los tamaños `B2ms` y `B1ls` (no solo busque especificaciones de infraestructura)?

   ![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part1/Diferencia1.png)
   
   ![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part1/Diferencia2.png)

9. ¿Aumentar el tamaño de la VM es una buena solución en este escenario?, ¿Qué pasa con la FibonacciApp cuando cambiamos el tamaño de la VM?

   - En este caso, es una solución que puede ayudar en los tiempos de ejecución del programa visiblemente. Sin embargo, toca revisar que la implementación de Fibonacci, pues esta no es la mejor 
   
9. ¿Qué pasa con la infraestructura cuando cambia el tamaño de la VM? ¿Qué efectos negativos implica?
   - Implica que se tiene que reiniciar toda la máquina y su infraestructura para poder ver el cambio de tamaño. Esto dejará por un tiempo sin servicio a todos aquellos que necesiten de los recursos de la máquina virtual y no se podrá atender las peticiones de los usuarios 

10. ¿Hubo mejora en el consumo de CPU o en los tiempos de respuesta? Si/No ¿Por qué?
   - Si, debido a que la máquina virtual tiene mayor márgen de uso de recursos que puede destinar para los cálculos de la función.

11. Aumente la cantidad de ejecuciones paralelas del comando de postman a `4`. ¿El comportamiento del sistema es porcentualmente mejor?

   - Para esto, se pone en 4 consolas distintas, el comando para ejecutar 10 iteraciones de Postman

![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part1/Post1.png)

![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part1/Post2.png)

![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part1/Post3.png)

![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part1/Post4.png)

No hay ningún cambio en el tiempo de ejecución del programa a excepción de la cuarta consola, donde su tiempo máximo fue de 59s buscando la número Fibonacci pedido. De lo demás, la media de tiempo siempre está por los 18.6s y un máximo de 18.7s - 18.8s (Exceptuando cuarta consola)

### Parte 2 - Escalabilidad horizontal

#### Crear el Balanceador de Carga

Antes de continuar puede eliminar el grupo de recursos anterior para evitar gastos adicionales y realizar la actividad en un grupo de recursos totalmente limpio.

1. El Balanceador de Carga es un recurso fundamental para habilitar la escalabilidad horizontal de nuestro sistema, por eso en este paso cree un balanceador de carga dentro de Azure tal cual como se muestra en la imágen adjunta.

![](images/part2/part2-lb-create.png)

#### Creación del balanceador de carga

![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part2/CrearEquilibrador.png)

![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part2/ipFrontEnd.png)

2. A continuación cree un *Backend Pool*, guiese con la siguiente imágen.

![](images/part2/part2-lb-bp-create.png)

#### Configuración POOL de Backend
![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part2/GrupoBackEnd.png)

3. A continuación cree un *Health Probe*, guiese con la siguiente imágen.

![](images/part2/part2-lb-hp-create.png)

4. A continuación cree un *Load Balancing Rule*, guiese con la siguiente imágen.

![](images/part2/part2-lb-lbr-create.png)

#### Implementación de regla de equilibrio de carga

![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part2/ReglaEquilibrio.png)

5. Cree una *Virtual Network* dentro del grupo de recursos, guiese con la siguiente imágen.

![](images/part2/part2-vn-create.png)


#### Creación de red virtual 

![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part2/Red1.png)


#### Crear las maquinas virtuales (Nodos)

Ahora vamos a crear 3 VMs (VM1, VM2 y VM3) con direcciones IP públicas standar en 3 diferentes zonas de disponibilidad. Después las agregaremos al balanceador de carga.

1. En la configuración básica de la VM guíese por la siguiente imágen. Es importante que se fije en la "Avaiability Zone", donde la VM1 será 1, la VM2 será 2 y la VM3 será 3.

![](images/part2/part2-vm-create1.png)

2. En la configuración de networking, verifique que se ha seleccionado la *Virtual Network*  y la *Subnet* creadas anteriormente. Adicionalmente asigne una IP pública y no olvide habilitar la redundancia de zona.

![](images/part2/part2-vm-create2.png)

3. Para el Network Security Group seleccione "avanzado" y realice la siguiente configuración. No olvide crear un *Inbound Rule*, en el cual habilite el tráfico por el puerto 3000. Cuando cree la VM2 y la VM3, no necesita volver a crear el *Network Security Group*, sino que puede seleccionar el anteriormente creado.

![](images/part2/part2-vm-create3.png)

4. Ahora asignaremos esta VM a nuestro balanceador de carga, para ello siga la configuración de la siguiente imágen.

![](images/part2/part2-vm-create4.png)


#### CREACION DE MAQUINAS VIRTUALES
   - MAQUINA 1 
   
   ![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part2/VM1.png)
   
   - MAQUINA 3 

   ![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part2/VM3.png)

5. Finalmente debemos instalar la aplicación de Fibonacci en la VM. para ello puede ejecutar el conjunto de los siguientes comandos, cambiando el nombre de la VM por el correcto

```
git clone https://github.com/daprieto1/ARSW_LOAD-BALANCING_AZURE.git

curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.34.0/install.sh | bash
source /home/vm1/.bashrc
nvm install node

cd ARSW_LOAD-BALANCING_AZURE/FibonacciApp
npm install

npm install forever -g
forever start FibonacciApp.js
```

#### BASH DE PRIMERA MAQUINA
![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part2/Bash1.png)


#### BASH DE SEGUNDA MAQUINA
![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part2/Bash2.png)

#### BASH DE TERCERA MAQUINA
![Imágen 1](https://github.com/DiegoGonzalez2807/ARSW-LAB9/blob/main/images/Solution-Part2/Bash3.png)

Realice este proceso para las 3 VMs, por ahora lo haremos a mano una por una, sin embargo es importante que usted sepa que existen herramientas para aumatizar este proceso, entre ellas encontramos Azure Resource Manager, OsDisk Images, Terraform con Vagrant y Paker, Puppet, Ansible entre otras.

#### Probar el resultado final de nuestra infraestructura

1. Porsupuesto el endpoint de acceso a nuestro sistema será la IP pública del balanceador de carga, primero verifiquemos que los servicios básicos están funcionando, consuma los siguientes recursos:

```
http://52.155.223.248/
http://52.155.223.248/fibonacci/1
```

2. Realice las pruebas de carga con `newman` que se realizaron en la parte 1 y haga un informe comparativo donde contraste: tiempos de respuesta, cantidad de peticiones respondidas con éxito, costos de las 2 infraestrucruras, es decir, la que desarrollamos con balanceo de carga horizontal y la que se hizo con una maquina virtual escalada.

3. Agregue una 4 maquina virtual y realice las pruebas de newman, pero esta vez no lance 2 peticiones en paralelo, sino que incrementelo a 4. Haga un informe donde presente el comportamiento de la CPU de las 4 VM y explique porque la tasa de éxito de las peticiones aumento con este estilo de escalabilidad.

```
newman run ARSW_LOAD-BALANCING_AZURE.postman_collection.json -e [ARSW_LOAD-BALANCING_AZURE].postman_environment.json -n 10 &
newman run ARSW_LOAD-BALANCING_AZURE.postman_collection.json -e [ARSW_LOAD-BALANCING_AZURE].postman_environment.json -n 10 &
newman run ARSW_LOAD-BALANCING_AZURE.postman_collection.json -e [ARSW_LOAD-BALANCING_AZURE].postman_environment.json -n 10 &
newman run ARSW_LOAD-BALANCING_AZURE.postman_collection.json -e [ARSW_LOAD-BALANCING_AZURE].postman_environment.json -n 10
```

**Preguntas**

### ¿Cuáles son los tipos de balanceadores de carga en Azure y en qué se diferencian?
   #### Balanceador de carga interno( Privado ): Este balanceador de carga se encarga de equilibrar la carga de trafico de una red privada 

   #### Balanceador de carga publico: Este balanceador de carga se encarga de equilibrar la carga de trafico de redes publicas, especificamente de la carga       proveniente de internet.

   #### Balanceador de carga de puerta de enlace: Es un balanceador que se adapta a escenarios de alto rendimiento y alta disponibilidad con dispositivos virtuales de red (NVA) de terceros. Con las capacidades de Gateway Load Balancer, puede implementar, escalar y administrar NVA fácilmente.

### ¿Qué es SKU?

Representa una unidad de mantenimiento de existencias, lo cual significa que es un codigo unico asignado a un servicio o producto de Azure, el cual nos permite a nosotros como usuarios la posibilidad de comprar existencias de los mismos.

###  qué tipos hay y en qué se diferencian?
 - Estándar: Productos estandar los cuales se pueden vender de manera individual o en paquetes,
 - Ensamblaje: Aquellos productos que se deben ensamblar antes de un envio, todos los SKU deberan encontrarse dentro de una misma instalacion.
 - Virtual: No necesitan de una instalacion fisica evitando asi un nivel de inventario
 - Componente: Productos incluidos en paquetes, esamblajes y colecciones

### ¿Cuál es el propósito del *Backend Pool*?
El almacenamiento las direcciones IP de las máquinas virtuales conectadas al balanceador de carga, ademas de esto, el componente define el grupo de recursos que brindarán tráfico para una Load Balancing Rule determinada.

### ¿Cuál es el propósito del *Health Probe*?
Supervisión de el estado de la aplicación, este se utiliza para detectar fallos de una aplicación en un endpoint del backend; Las respuestas de Health Probe determinan qué instancias del backend pool recibirán nuevos flujos.

### ¿Cuál es el propósito de la *Load Balancing Rule*? 
El proposito del Load Balancing Rule es definir como se debera distribuir el trafico de las maquinas virtuales dentro del pool de backend.

### ¿Qué tipos de sesión persistente existen, por qué esto es importante y cómo puede afectar la escalabilidad del sistema?.
 - Ninguno(hash-based): Peticiones recurrentes de un mismo cliente podrían ser atendidas por máquinas diferentes del backend
 - IP del cliente: : Todas las peticiones que procedan de una misma IP de origen serás atendidas por la misma máquina del backend.
 - IP y protocolo del cliente: Todas las peticiones que procedan de una misma IP y puerto de origen serán atendidas por la misma máquina del backend, pero si vienen de la misma IP pero con un puerto de origen diferente podrían ser atendidas por otra máquina del backend.

### ¿Qué es una *Virtual Network*? 
 red que permite la interconexión de dispositivos y máquinas virtuales mediante software, independientemente de un ubicación fisica.

### ¿Qué es una *Subnet*? 
Segmentación de una red fisica o red virtual, estas segmentaciones contarán con su propio rango de direcciones ip según como se realice la partición de la dirección ip original

### ¿Para qué sirven los *address space* y *address range*?+
 - Address space: Cuando se crea una red virtual, se debe de especificar un rango de direcciones ip las cuales no se superponen unas con otras
 - Address range: Determina el numero de direcciones que se tienen o se pueden tener en un address space y dependiendo de la cantidad de recursos que se necesiten en la red virtual


### ¿Qué son las *Availability Zone* y por qué seleccionamos 3 diferentes zonas?. ¿Qué significa que una IP sea *zone-redundant*?
 - Una availability zone, son zonas que buscan garantizar una alta disponibilidad replicando sus aplicaciones y datos con el fin de protegerlos de puntos de fallo, estas zonas se encuentran dentro de una region. Se selecciona 3 zonas de disponibilidad para garantizar una mejor disponibilidad y tolarancia a fallos dentro del sistema.
 - Una ip zone-redundant azure separa física y lógicamente el gateway dentro de una region, lo cual permite mejorar la conectividad de la red privada y disminuye fallos a nivel de zona de disponibilidad
 
### ¿Cuál es el propósito del *Network Security Group*?
 lograr filtrar el trafico desde y hacia los recursos de una red virtual de Azure






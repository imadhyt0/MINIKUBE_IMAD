# Proyecto de Servicios en Red - Infraestructura Web Segura

En este repositorio se encuentran los archivos de configuración de mi proyecto de kuberbetes. El objetivo de la práctica ha sido desplegar una aplicación web en Kubernetes, la aplicación consiste en una tienda de vinilos donde se pueden añadir discos a una base de datos MySQL y ver el inventario.
Primero hemos trabajado en local con Minikube para hacer pruebas, y después lo hemos desplegado en AWS EKS.

## 1. Desarrollo en máquina virtual (Minikube)
Al principio trabajamos en una máquina virtual con Minikube para comprobar que todo funcionaba correctamente antes de subirlo a AWS.
Creamos los archivos YAML para cada servicio:
* **MySQL (base de datos)**
* **PHP (backend)**
* **NGINX (frontend)**
* **ConfigMap con el código de la aplicación**

Aplicamos la configuracion con:
```bash
kubectl apply -f archivo.yml
```

Al principio tenia pensado subir el proyecto charneco donde habiamos creado una pagina web en localhost (XAMPP) con mysql y php, pero por problemas del pasado perdi dicha practica, pero aun asi he realizado una version simplificada del proyecto.
Y mas adelante si hay tiempo me gustaria aplicarlo a mi proyecto de TFG que estoy realizacion con Hector




## 2. Paso a AWS EKS
Una vez funcionando en Minikube, creamos el clúster en AWS EKS.
Con tu documentacion del proceso de crear un cluster en AWS con nodos documentada ha sido muy sencillo realizar esa parte del proceso y ya una vez que han sido creados los nodos solo faltaba la parte de poner los credenciales de aws y el siguiente comando
```bash
aws eks update-kubeconfig --region us-east-1 --name nombre_De_tu_cluster
```

Una vez realizada la conexion con mi cluster, procedemos a subir los archivos yaml como en localhost mediante el mismo comando que en la parte 1
```bash
kubectl apply -f archivo.yml
```
### Problema volumen
En su momento tambien se hablo en clase, a la hora de crear un volumen con una practica de las tuyas tuvimos problemas ya que no podiamos crear volumenes persistentes(se queda en pending), e investigando un poco era debido a que AWS no te da permisos para hacerlo, por lo que para la practica no es tan necesario, pero en un entorno real si lo es

Para demostrar que la conexión es segura y no se pueden robar las contraseñas, en el vídeo realizo un snifado de red con `tcpdump` donde se ve que las cabeceras y el tráfico están totalmente cifrados.

### Acceso a la aplicación
Configuramos el servicio de NGINX como tipo LoadBalancer, lo que nos dio una URL pública de AWS.
Gracias a esto pudimos acceder a la aplicación desde el navegador.

## 3. HTTPS
En este proyecto se ha implementado HTTPS utilizando NGINX con un certificado TLS.

Inicialmente se intentó utilizar certificados válidos mediante AWS Certificate Manager (ACM), pero no fue posible debido a las limitaciones de AWS Academy, ya que no se dispone de control sobre DNS ni acceso a servicios como Route53 necesarios para validar el dominio.

También se intentó usar un dominio externo (DuckDNS), pero no fue viable porque no permite configurar los registros DNS necesarios para la validación del certificado en AWS.

Por este motivo, se optó por utilizar un certificado autofirmado configurado directamente en NGINX.

Aunque el navegador indica que la conexión no es segura, esto se debe únicamente a que el certificado no está firmado por una autoridad certificadora reconocida. Sin embargo, el cifrado HTTPS funciona correctamente, ya que el tráfico entre el cliente y el servidor está protegido mediante TLS.

Para verificar esto, se ha realizado una captura de tráfico con Wireshark, donde se puede observar que los datos viajan cifrados y no son visibles en texto plano.

En un entorno real, la solución adecuada sería utilizar un dominio propio junto con AWS Certificate Manager o Let's Encrypt para disponer de un certificado válido y reconocido.

### Verificacion con WIRESHARK
He descargado en la maquina virtual el servicio wireshark y he filtrado el trafico para ver si los datos viajan cifrados (TLS), ya que aunque aya tenido que recurrir a un certificado autofirmado podemos ver que la informacion no viaja en texto plano

En la captura de Wireshark se puede observar el protocolo TLSv1.3, incluyendo mensajes como “Client Hello” y “Server Hello”, lo que demuestra que la comunicación se realiza mediante HTTPS. Esto confirma que los datos viajan cifrados y no en texto plano.
![texto alternativo descriptivo](imagenes/wireshark.PNG)



## 4. Vídeo Demostrativo
En el siguiente enlace dejo el vídeo donde explico paso a paso el funcionamiento de todo este montaje, probando todas las casuísticas que se pedían en la práctica:

**[[PONER AQUÍ EL ENLACE AL VÍDEO](https://drive.google.com/file/d/1pg5p7hHuqL6YbKSF11ewI2rsxrvGCFjw/view?usp=sharing)]**


# RootMe-THM
Máquina RootMe de TryHackMe en Español
<img src='https://github.com/user-attachments/assets/0eff2b42-89aa-4a0a-8bfa-998bc57b21b1'></img>
"RootMe" es una máquina en TryHackMe diseñada para desafiar tu ingenio y creatividad al pensar como un atacante. Con un enfoque didáctico, esta máquina es ideal tanto para principiantes como para usuarios de nivel intermedio.
Utilizamos una variedad de herramientas fundamentales: Nmap, Wfuzz, Wappalyzer, Curl, Netcat, Burp Suite, etc.

-------------------------------------------
## Fase de Reconocimiento
Realizar un reconocimiento sirve para comprender el entorno objetivo. En la máquina "RootMe" de TryHackMe, comenzamos con la fase de Reconocimiento para identificar qué servicios están expuestos y qué puntos de entrada podrían ser explotados.
El comando `ping` para verificar que la máquina objetivo está en línea y responde a una traza ICMP
```
ping -c 1 10.10.244.159

PING 10.10.244.159 (10.10.244.159) 56(84) bytes of data.
64 bytes from 10.10.244.159: icmp_seq=1 ttl=61 time=185 ms
```
El resultado muestra que la máquina está activa, con un `TTL` de `61`, lo que indica que es muy probablemente un sistema Linux. Vamos a identificar los puertos abiertos en la máquina utilizando `nmap`.
```
nmap -p- --open -sS --min-rate 2000 -vvv -n -Pn -oN rmPorts 10.10.244.159

PORT   STATE SERVICE REASON
22/tcp open  ssh     syn-ack ttl 61
80/tcp open  http    syn-ack ttl 61
```
Este comando realiza un escaneo completo de todos los puertos `-p-`, enfocándose solo en aquellos que están abiertos `--open`. Además, el uso de -Pn desactiva el escaneo de host y `--min-rate 2000` asegura un escaneo un poco más rápido.
- Puerto 22/tcp: Servicio SSH
- Puerto 80/tcp: Servicio HTTP

Los puertos abiertos (22 y 80) en la máquina "RootMe", se debe obtener más detalles sobre los servicios que están corriendo en esos puertos. Esto nos permitirá identificar versiones específicas que podrían tener vulnerabilidades conocidas.
```
nmap -p22,80 -sCV -oN rmVersion 10.10.244.159

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 4a:b9:16:08:84:c2:54:48:ba:5c:fd:3f:22:5f:22:14 (RSA)
|   256 a9:a6:86:e8:ec:96:c3:f0:03:cd:16:d5:49:73:d0:82 (ECDSA)
|_  256 22:f6:b5:a6:54:d9:78:7c:26:03:5a:95:f3:f9:df:cd (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
|_http-title: HackIT - Home
|_http-server-header: Apache/2.4.29 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```
Este comando escanea los puertos 22 y 80, utilizando scripts estándar `-sC` y detección de versiones `-sV`. Dado que el puerto 22 está abierto, se puede investigar más sobre posibles credenciales para SSH, pero el foco principal será investigar el servicio HTTP en el puerto 80.

Configuración y administración de un DNS autoritativo mediante BIND9 en Docker

1. Descripción del proyecto

El presente proyecto consiste en configurar un servidor DNS autoritativo utilizando BIND9 dentro de un contenedor Docker.

Se creó una zona de resolución local llamada empresa.local, donde se configuraron diferentes registros DNS para relacionar nombres de dominio con direcciones IPv4.

El objetivo es comprender el funcionamiento de un servidor DNS, administrar una zona y verificar su funcionamiento mediante herramientas de diagnóstico.

2. Estructura del proyecto
text
dns-bind9-docker/
│
├── config/
│   ├── named.conf
│   ├── named.conf.local
│   └── db.empresa.local
│
├── docker-compose.yml
└── README.md

3. ¿Qué contiene un archivo de zona DNS?

Un archivo de zona DNS contiene los registros que permiten relacionar nombres de dominio con diferentes recursos de una red.

En este proyecto se creó el archivo db.empresa.local, que contiene:

SOA: indica la autoridad y los parámetros administrativos de la zona.
NS: identifica el servidor DNS autoritativo.
A: relaciona un nombre de dominio con una dirección IPv4.
TTL: establece el tiempo de almacenamiento de los registros en caché.

Estos registros permiten que los dispositivos puedan consultar y resolver los nombres configurados.

4. ¿Por qué se utiliza el Serial SOA?

El Serial SOA es un número de versión que permite identificar las modificaciones realizadas en una zona DNS.

Cada vez que se modifica el archivo de zona, se debe incrementar el número de serial.

Esto permite que los servidores DNS secundarios puedan identificar si existe una versión más reciente de la zona y actualizar sus registros cuando corresponda.

En este proyecto se utilizó el serial:

2026112101

5. Registros DNS implementados

Se configuraron los siguientes registros:

Nombre	Tipo	Dirección IP
empresa.local	A	192.168.1.100
www.empresa.local	A	192.168.1.100
dns.empresa.local	A	192.168.1.100
servidor.empresa.local	A	192.168.1.101
6. ¿Cómo comprobar los registros DNS?

Para verificar el funcionamiento del servidor se utilizaron las herramientas named-checkconf, named-checkzone y dig.

Comprobar la configuración
bash
docker exec servidor-dns named-checkconf

Comprobar la zona
bash
docker exec servidor-dns named-checkzone empresa.local /etc/bind/db.empresa.local

Consultar un registro DNS
bash
docker exec servidor-dns dig @127.0.0.1 www.empresa.local A

Consultar los servidores autoritativos
bash
docker exec servidor-dns dig @127.0.0.1 empresa.local NS


Una respuesta correcta debe mostrar el estado NOERROR y la dirección IP correspondiente en la sección ANSWER SECTION.

La bandera aa permite identificar que la respuesta es autoritativa.

7. ¿Por qué DNS por sí solo no publica una aplicación web?

DNS es un sistema encargado de traducir nombres de dominio en direcciones IP.

Sin embargo, no almacena ni proporciona el contenido de las páginas web.

Para publicar una aplicación web también es necesario contar con un servidor web como Apache o Nginx, donde se encuentre alojada la aplicación.

Además, deben estar configurados los puertos HTTP (80) o HTTPS (443), la conectividad de red y los permisos de acceso correspondientes.

Por esta razón, un servidor DNS puede resolver correctamente un dominio, pero eso no significa que exista una aplicación web disponible en esa dirección.

8. Conclusión

Mediante este proyecto se aprendió a configurar un servidor DNS autoritativo utilizando BIND9 y Docker.

También se comprendió la importancia de los archivos de configuración, los registros DNS, el número Serial SOA y las herramientas de diagnóstico.

Finalmente, se comprobó que DNS cumple una función diferente a la de un servidor web, ya que su responsabilidad principal es resolver nombres de dominio y no publicar contenido web.
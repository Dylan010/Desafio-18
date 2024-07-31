# Desafío 18 - Ethical Hacking

Este repositorio contiene la solución al Desafío 18 de Ethical Hacking, enfocado en realizar pruebas de seguridad sobre sistemas informáticos, específicamente en el sitio http://vulnweb.com/.

## Objetivo

Realizar pruebas de seguridad éticas para:
1. Identificar debilidades en la infraestructura.
2. Descubrir vulnerabilidades potenciales.
3. Practicar técnicas de ethical hacking de manera responsable.

## Requisitos

- Sistema operativo: Manjaro Linux (o cualquier distribución basada en Arch)
- Herramientas: subfinder, dig, whois, geoip-lookup, nmap

## Pasos del Desafío

### 1. Identificar sitios web hosteados en http://vulnweb.com/

Usaremos subfinder para esta tarea.

Instalación:
```bash
sudo pacman -S go
go install -v github.com/projectdiscovery/subfinder/v2/cmd/subfinder@latest
export PATH=$PATH:~/go/bin
```

Uso:
```bash
subfinder -d vulnweb.com -o subdomains.txt
```

### 2. Obtener información del dominio principal

Usaremos whois y dig para esta tarea.

```bash
whois vulnweb.com > whois_info.txt
dig vulnweb.com ANY +noall +answer > dig_info.txt
```

### 3. Identificar direcciones IP de los sitios hospedados

Crearemos un script para obtener las IPs de los subdominios:

```bash
#!/bin/bash

echo "Subdomain IPs for vulnweb.com:" > ip_results.txt
echo "--------------------------------" >> ip_results.txt

while read subdomain; do
    echo "Subdomain: $subdomain" >> ip_results.txt
    dig +short $subdomain >> ip_results.txt
    echo "-------------------" >> ip_results.txt
done < subdomains.txt

cat ip_results.txt
```

Guarda este script como `get_ips.sh`, dale permisos de ejecución y ejecútalo:

```bash
chmod +x get_ips.sh
./get_ips.sh
```

### 4. Identificar la geolocalización de cada dirección IP

Instala geoip-lookup:

```bash
sudo pacman -S geoip
```

Usa el siguiente script para obtener la geolocalización:

```bash
#!/bin/bash

echo "Geolocation of IPs:" > geolocation_results.txt
echo "---------------------" >> geolocation_results.txt

grep -oE "\b([0-9]{1,3}\.){3}[0-9]{1,3}\b" ip_results.txt | sort -u | while read ip; do
    echo "IP: $ip" >> geolocation_results.txt
    geoiplookup $ip >> geolocation_results.txt
    echo "---------------------" >> geolocation_results.txt
done

cat geolocation_results.txt
```

Guarda este script como `get_geolocation.sh`, dale permisos de ejecución y ejecútalo:

```bash
chmod +x get_geolocation.sh
./get_geolocation.sh
```

### 5. Obtener información adicional (puertos abiertos, etc.)

Usaremos nmap para esta tarea:

```bash
sudo pacman -S nmap
nmap -sV vulnweb.com > nmap_results.txt
```

## Documentación

Asegúrate de documentar todos los pasos realizados, incluyendo:
- Comandos utilizados
- Capturas de pantalla de los resultados
- Problemas encontrados y soluciones aplicadas


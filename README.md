Paso 1: Crear dos máquinas virtuales
VM A (Cliente) → Puerto SSH 2222
VM B (Servidor) → Puerto SSH 2223
Ambas deben estar en la misma red interna.

Paso 2: Crear usuarios
En VM A:
sudo adduser Alex
En VM B:
sudo adduser Brais

Paso 3: Conexión SSH de A hacia B
En VM A (como usuario Alex):
su - Alex
ssh -p 2223 Brais@<IP_VM_B>
Se almacena la clave del servidor en ~/.ssh/known_hosts.

Paso 4: Crear directorio en A y enviarlo a B
En VM A:
mkdir /tmp/prueba
echo "Archivo desde máquina A" > /tmp/prueba/prueba.txt
Enviar a B:
scp -P 2223 -r /tmp/prueba Brais@<IP_VM_B>:/tmp/

Paso 5: Crear directorio en B y enviarlo a A
En VM B:
mkdir /tmp/prueba2
echo "Archivo desde máquina B" > /tmp/prueba2/prueba2.txt
Enviar a A:
scp -P 2222 -r /tmp/prueba2 Alex@<IP_VM_A>:/tmp/

Paso 6: Transferir los directorios al escritorio del anfitrión
Desde el equipo anfitrión:
scp -P 2222 -r Alex@localhost:/tmp/prueba ~/Escritorio/
scp -P 2223 -r Brais@localhost:/tmp/prueba2 ~/Escritorio/

Paso 7: Crear directorio con 200 archivos en B y enviarlo al host
En VM B:
mkdir /tmp/prueba3
for i in {1..200}; do touch /tmp/prueba3/archivo$i.txt; done
Desde el anfitrión:
scp -P 2223 -r Brais@localhost:/tmp/prueba3 ~/Escritorio/

Paso 8: Conexión SSH con clave pública
En VM A (como Alex):
ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa -C "clave ssh" -N servidorssh
Copiar la clave pública al servidor:
ssh-copy-id -p 2223 -i ~/.ssh/id_rsa.pub Brais@<IP_VM_B>
Probar conexión:
ssh -p 2223 Brais@<IP_VM_B>



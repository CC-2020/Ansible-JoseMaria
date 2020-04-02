# Tarea Ansible Google Cloud Platform
Se va a proceder a crear una instancia en GCP, donde se instalará la herramienta Ansible. 

También se crearán dos instancias más cuya configuración será establecida desde la primera instancia a través de la herramienta Ansible.

## Pasos a seguir
Se va a mostrar a continuación los pasos que se han seguido para realizar la tarea.

## Creación de las instancias
Las tres instancias se han creado con las siguientes características:
* **Tipo**: f1-micro.
* **S.O.**: Ubuntu 18.04.
* **Red**: "default".
* **Firewall**: Permitir tráfico SSH y HTTP.
* **Zona**: us-central1-a.

Las direcciones IPs de las instancias secundarias son: 10.128.0.3 // 10.128.0.4

En las Instancias secundarias se han tenido que generar claves SSH para que la instancia principal se pudiese conectar a ellas a través de Ansible.

## Instalación de Ansible
La herramienta Ansible se instalará únicamente en la instancia principal:

        $ sudo apt update
        $ sudo apt install software-properties-common
        $ sudo apt-add-repository ppa:ansible/ansible
        $ sudo apt install ansible
        
## Configuración de Ansible
A continuación, se instarán las dos instancias secundarias en el fichero hosts de Ansible. Es decir, crearemos un inventario:

        $ sudo nano /etc/ansible/hosts
        
         [servers]
         instancia1 ansible_host=10.128.0.3
         instancia2 ansible_host=10.128.0.4
        
## Comprobación correcta instalación
Para comprobar la correcta instalación de la herramienta podemos comprobar que los siguientes comandos funcionan correctamente:

        $ ansible servers -a /bin/date
        (Nos devuelve la fecha y hora de las instancias secundarias)
        
        $ ansible all --list-hosts
        (Nos muestra la lista de hosts del fichero /etc/ansible/hosts)
        

## Creación de la Configuración
A continuación se creará un Playbook para realizar la instalación de Apache, PHP y MySQL en las instancias secundarias:

        $ nano instalar-servicios.yml 

        ---
          - hosts: servers
            become: true
            tasks:
              - name: Instalacion servicios
              apt:
                name:
                  - php
                  - apache2
                  - mysql-server
                state: present
              - name: Asegurar que apache se inicia
                service: name=apache2 state=started enabled=yes
                
                
## Ejecución del Playbook
Para ejecutar el Playbook ejecutamos la siguiente instrucción:

        $ ansible-playbook instalar-servicios.yml
        
## Comprobación correcto funcionamiento
Si todo ha salido correctamente, podríamos navegar a la IP EXTERNA de ambas instancias secundarias y nos saldría la web de bienvenida de Apache.

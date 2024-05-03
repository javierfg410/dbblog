# DB Blog
## Instalación de entorno Prestashop para las pruebas

- Crear carpeta de proyecto
- Crear archivo 'docker-compose.yml' dentro de la carpeta del proyecto:
- Copiar el siguiente contenido en el archivo 'docker-compose.yml':

  ```
  services:
    mysql:
        image: mysql:5.7
        container_name: prestashop-mysql
        tty: true
        ports:
            - "3308:3306"
        volumes:
            - "./var/lib/mysql/:/var/lib/mysql"
        environment:
            MYSQL_ROOT_PASSWORD: root
        networks:
            - cx-prestashop1.7-net
    phpmyadmin:
        image: phpmyadmin/phpmyadmin
        container_name: PhpMyAdmin
        environment:
            PMA_HOST: prestashop-mysql
            PMA_PORT: 3308
            PMA_ARBITRARY: 1
        restart: always
        ports:
            - "8082:80"
        networks:
            - cx-prestashop1.7-net
    server:
        image: prestashop/prestashop:1.7
        container_name: PS-apache
        ports:
            - "8083:80"
        volumes:
            - "./:/var/www/html"
        environment:
            DB_SERVER: prestashop-mysql
        depends_on:
            - mysql
        networks:
            - cx-prestashop1.7-net
   networks:
       cx-prestashop1.7-net:
           driver: bridge

- Establecer contraseña para mysql ( MYSQL_ROOT_PASSWORD: XXXXXX )
- Ejecutar el comando 'docker compose up -d' para levantar el servidor (tardará la primera vez)
- Realizar la instalación de prestashop, accediendo a 'localhost:8080'. En la configuración de la base de datos, el servidor es el nombre del contenedor de mysql 'PS-MySql'.
- Clonar este repositorio en la carpeta modules, dentro de prestashop, con el nombre 'dbjointpurchase'
- En el back office de prestashop, instalar este módulo.

## Descripción del módulo ##

Para mejorar la usabilidad y trabajo del cliente, quiere poder crear artículos del blog y que se publiquen en una fecha que el cliente configure.
Desarrollar la funcionalidad para PrestaShop 1.7, en una actualización del módulo para que el cliente pueda guardar la fecha deseada de publicación y el post no se publique hasta que no llegue dicha fecha

## Modificaciones ##
-  ### classes/DbBlogPost.php ###
     -  creamos nuevo campo de fecha de publicación
     -  Añadimos el campo al array del formulario
     -  add(). Asignamos la fecha de publicacion programada si se ha establecido
     -  update(). Asignamos la fecha de publicacion programada si se ha establecido
     -  Añadimos el campo nuevo a las sql necesarias.
     -  Correcciones de codigo para evitar errores y refactorizaciones menores para mejorar la legibilidad del código.
- ### classes/DbBlogCategory.php ###
    -  Añadimos el campo nuevo a las sql necesarias.
    -  Correcciones de codigo para evitar errores y refactorizaciones menores para mejorar la legibilidad del código.
-  ### controller/admin/AdminDbBlogPostController ###
    -  Correcciones de codigo para evitar errores y refactorizaciones menores para mejorar la legibilidad del código.
    - renderForm(). Añadimos el campo de fecha de publicación al formulario. Tambien lo formateamos para que se muestre correctamente.
-  ### controller/front/dbpost ###
    - initContent(). Añadimos la condición para que solo se muestren los posts que tengan una fecha de publicación superior a la fecha actual.
- ### sql/* ###
    - Creamos/Borramos las nuevas tablas en BD que se encargaran de guardar la configuración del cliente.
   
## Funcionamiento general del módulo ##
- El módulo añade un campo de fecha en la configuración del post, en el back office.
- Solo se mostraran los posts que tengan una fecha de publicación superior a la fecha actual.
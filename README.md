# Integrantes:
## David Narváez - Cristian Rodríguez

# Laboratorio 5 - INTRODUCCIÓN A PROYECTOS WEB - 2022-1

## PARTE I - JUGANDO A SER UN CLIENTE HTTP

1. Abra una terminal Linux o consola de comandos Windows.

2. Realizando una conexión síncrona TCP/IP a través de Telnet

Comando :

![image](https://github.com/CrisRod8/Laboratorio-5-CVDS/blob/main/pics/u1.png)

3. Realizando consultas

GET /sssss/abc.html HTTP/1.0

Host: escuelaing.edu.co    

Resultado: 

![image](https://github.com/CrisRod8/Laboratorio-5-CVDS/blob/main/pics/u2.jpg)

Como podemos ver en la imagen tenemos un código de error 301, lo que significa que el recurso ha sido movido permanentemente.

¿Qué otros códigos de error existen?, ¿En qué caso se manejarán?

Tenemos los errores 300 que representan errores de redirección.

Tenemos los errores 400 que indican errores que se generan por parte del cliente.

Y finalmente los errores 500 que significan errores que se producen por parte del servidor.

4. Realizando nueva conexión a telnet.

Comando :

GET /html  HTTP/1.1
Host: httpbin.org

![image](https://github.com/CrisRod8/Laboratorio-5-CVDS/blob/main/pics/u3.png)

Resultado:

![image](https://github.com/CrisRod8/Laboratorio-5-CVDS/blob/main/pics/u4.png)

Nos muestra un mensaje de éxito 200 y el contenido en formato html.

5. Contando palabras.

Se copia el contenido html obtenido con el comando: CTRL + SHIFT + C.

Se crea y pega el contenido en un archivo que llamamos contenido.txt

Con el comando wc -c se puede obtener la cantidad de caracteres del contenido del archivo contenido.txt.

![image](https://github.com/CrisRod8/Laboratorio-5-CVDS/blob/main/pics/u5.png)

En este caso se obtuvo un total de 3743 caracteres.


¿Cuál es la diferencia entre GET y POST?

Los métodos GET y POST son protocolos HTTP los cuales se envían al servidor como petición y reciben una respuesta a dicha solicitud.

Usando GET podemos obtener información del servidor, trayendo datos que están almacenadas en el servidor. Con POST podemos enviar información desde el cliente para que sea procesada y actualizada en el servidor.

¿Qué otros tipos de peticiones existen?

Además de GET y POST tenemos peticiones útiles como:

HEAD : Pide una respuesta como en la petición GET, pero este solo retorna el encabezado sin el cuerpo de la respuesta.

PUT : Es usado para solicitar que el servidor almacene el cuerpo de la entidad en una ubicación específica dada por URL.

DELETE : Es utilizada para solicitar al servidor que elimine un archivo en una ubicación específica dada por URL.

CONNECT : Es usada para establecer una conexión de red con un servidor web mediante HTTP.

6. En la practica no se utiliza telnet para hacer peticiones a sitios web sino el comando curl con ayuda de la linea de comandos:

Comando curl :

curl www.httpbin.org 

![image](https://github.com/CrisRod8/Laboratorio-5-CVDS/blob/main/pics/u6.png)

Comando curl -v: 

curl -v www.httpbin.org

![image](https://github.com/CrisRod8/Laboratorio-5-CVDS/blob/main/pics/u7.png)


Con el comando curl -v podemos obtener el encabezado de la solicitud y el número de la respuesta obtenida. También hará que muestre todos los encabezados que envía y recibe. 

Con el comando curl -i nos incluye el encabezado HTTP en la salida, en este encabezado HTTP se incluye el nombre del servidor, fecha del documento, versión HTTP etc.


## PARTE II. - HACIENDO UNA APLICACIÓN WEB DINÁMICA A BAJO NIVEL.

Para esto, cree un proyecto maven nuevo usando el arquetipo de aplicación Web estándar maven-archetype-webapp y realice lo siguiente:

1. Revise la clase SampleServlet incluida a continuacion, e identifique qué hace:

~~~
package edu.eci.cvds.servlet;

import java.io.IOException;
import java.io.Writer;
import java.util.Optional;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@WebServlet(
    urlPatterns = "/helloServlet"
)
public class SampleServlet extends HttpServlet{
    static final long serialVersionUID = 35L;

    @Override
   protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
       Writer responseWriter = resp.getWriter();
       Optional<String> optName = Optional.ofNullable(req.getParameter("name"));
       String name = optName.isPresent() && !optName.get().isEmpty() ? optName.get() : "";

       resp.setStatus(HttpServletResponse.SC_OK);
       responseWriter.write("Hello" + name + "!");
       responseWriter.flush();
   }
}
~~~

Revise qué valor tiene el parámetro ‘urlPatterns’ de la anotación @WebServlet, pues este indica qué URLs atiende las peticiones el servlet.

2. En el pom.xml, modifique la propiedad "packaging" con el valor "war". Agregue la siguiente dependencia:
~~~
<dependency>
     <groupId>javax</groupId>
     <artifactId>javaee-web-api</artifactId>
     <version>7.0</version>
     <scope>provided</scope>
</dependency>
y agregue la seccion build al final del tag project en el archivo pom.xml:

<build>
   <plugins>
       <plugin>
           <groupId>org.apache.maven.plugins</groupId>
           <artifactId>maven-compiler-plugin</artifactId>
           <version>3.8.0</version>
           <configuration>
               <source>1.8</source>
               <target>1.8</target>
           </configuration>
       </plugin>
       <plugin>
           <groupId>org.apache.maven.plugins</groupId>
           <artifactId>maven-war-plugin</artifactId>
           <version>2.3</version>
           <configuration>
               <failOnMissingWebXml>false</failOnMissingWebXml>
           </configuration>
       </plugin>
       <plugin>
           <groupId>org.apache.maven.plugins</groupId>
           <artifactId>maven-dependency-plugin</artifactId>
           <version>2.6</version>
           <executions>
               <execution>
                   <phase>validate</phase>
                   <goals>
                       <goal>copy</goal>
                   </goals>
                   <configuration>
                       <silent>true</silent>
                       <artifactItems>
                           <artifactItem>
                               <groupId>javax</groupId>
                               <artifactId>javaee-endorsed-api</artifactId>
                               <version>7.0</version>
                               <type>jar</type>
                           </artifactItem>
                       </artifactItems>
                   </configuration>
               </execution>
           </executions>
       </plugin>

       <!-- Tomcat embedded plugin. -->
       <plugin>
           <groupId>org.apache.tomcat.maven</groupId>
           <artifactId>tomcat7-maven-plugin</artifactId>
           <version>2.2</version>
           <configuration>
               <port>8080</port>
               <path>/</path>
           </configuration>
       </plugin>
   </plugins>
</build>
~~~

3. Revise en el pom.xml para qué puerto TCP/IP está configurado el servidor embebido de Tomcat (ver sección de plugins).
4. Compile y ejecute la aplicación en el servidor embebido Tomcat, a través de Maven con:
~~~
mvn package
~~~
![](Img/mvn%20package%20lab05.png)
~~~
mvn tomcat7:run
~~~
![](Img/mvn%20tomcatrun%20lab05.png)

5. Abra un navegador, y en la barra de direcciones ponga la URL con la cual se le enviarán peticiones al ‘SampleServlet’. Tenga en cuenta que la URL tendrá como host ‘localhost’, como puerto, el configurado en el pom.xml y el path debe ser el del Servlet. Debería obtener un mensaje de saludo.
   
![](Img/nav%20hello%20lab05.png)

6. Observe que el Servlet ‘SampleServlet’ acepta peticiones GET, y opcionalmente, lee el parámetro ‘name’. Ingrese la misma URL, pero ahora agregando un parámetro GET (si no sabe como hacerlo, revise la documentación en http://www.w3schools.com/tags/ref_httpmethods.asp).

![](Img/nav%20hello%20world%20lab05.png)
7. Busque el artefacto gson en el repositorio de maven y agregue la dependencia.

~~~
<dependency>
    <groupId>com.google.code.gson</groupId>
    <artifactId>gson</artifactId>
    <version>2.9.0</version>
</dependency>
~~~
8. En el navegador revise la dirección https://jsonplaceholder.typicode.com/todos/1. Intente cambiando diferentes números al final del path de la url.

![](Img/Json%20lab05.png)

9. Basado en la respuesta que le da el servicio del punto anterior, cree la clase edu.eci.cvds.servlet.model.Todo con un constructor vacío y los métodos getter y setter para las propiedades de los "To Dos" que se encuentran en la url indicada.

![image](https://user-images.githubusercontent.com/65261708/158000217-6065c886-b4a1-4845-8910-2690768054de.png)

10. Utilice la siguiente clase para consumir el servicio que se encuentra en la dirección url del punto anterior:

![image](https://user-images.githubusercontent.com/65261708/158000308-3eb93db8-ff36-4d43-985f-13698cedd998.png)

11. Cree una clase que herede de la clase HttpServlet (similar a SampleServlet), y para la misma sobrescriba el método heredado doGet. Incluya la anotación @Override para verificar –en tiempo de compilación- que efectivamente se esté sobreescribiendo un método de las superclases.

![image](https://user-images.githubusercontent.com/65261708/158000371-1943a5ab-054c-420e-9e5a-26b9b1923c72.png)

12. Para indicar en qué URL el servlet interceptará las peticiones GET, agregue al método la anotación @WebServlet, y en dicha anotación, defina la propiedad urlPatterns, indicando la URL (que usted defina) a la cual se asociará el servlet.

![image](https://user-images.githubusercontent.com/65261708/158000431-1e7ecf1f-8bd1-4b14-b5f0-b55e4e44c6a8.png)

13. Teniendo en cuenta las siguientes métodos disponibles en los objetos ServletRequest y ServletResponse recibidos por el método doGet

![image](https://user-images.githubusercontent.com/65261708/158003726-c534026b-2819-43e6-bbd9-e4adb59f901f.png)

## PARTE III

16. En su servlet, sobreescriba el método doPost, y haga la misma implementación del doGet.

![image](https://user-images.githubusercontent.com/65261708/158003884-1321c7b8-3068-4fca-ba49-7b367e69cc1f.png)

18. En la página anterior, cree un formulario que tenga un campo para ingresar un número (si no ha manejado html antes, revise http://www.w3schools.com/html/ ) y un botón. El formulario debe usar como método ‘POST’, y como acción, la ruta relativa del último servlet creado (es decir la URL pero excluyendo ‘http://localhost:8080/’). Revise este ejemplo de validación de formularios con javascript y agruéguelo a su formulario, de manera que -al momento de hacer ‘submit’- desde el browser se valide que el valor ingresado es un valor numérico.

![image](https://user-images.githubusercontent.com/65261708/158004165-48db73f9-c083-41e5-bb30-fc4cb97d4d7a.png)

En la captura se evidencian los cambios requeridos en el punto 18.



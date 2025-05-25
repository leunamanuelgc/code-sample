# PRÁCTICA OBLIGATORIA DESARROLLO DE APLICACIONES DISTRIBUIDAS: Aplicación distribuida para la gestión de un centro de procesamiento de datos
Creado por:
<table>
  <tbody>
    <tr>
      <th align="center">Alumno</th>
      <th align="center">Tareas realizadas</th>
      <th align="center">Commits</th>
      <th align="center">Ficheros</th>
    </tr>
    <tr>
      <td align="left">Manuel Gutiérrez Castro</td>
      <td align="left">
        <ul>
          <li>Gestión de los mensajes asíncronos entre servicios</li>
          <li>Formulario para creación de discos e instancias con HTML, JS y AJAX</li>
          <li>Participación en la creación de entidades</li>
          <li>Participación en la API REST y en los servicios</li>
          <li>Participación en los controladores y endpoints</li>
					<li>Creación del README.md y los diagramas de clase y UML</li>
        </ul>
      </td>
      <td align="left">
        <ul>
          <li>"Added full Disk and Instance creation functionality"</li>
          <li>"Added AMQP for disks creation"</li>
          <li>"Added RabbitMQConfig, and started queueing implementation"</li>
          <li>"Add disk-instance OneToOne relation"</li>
          <li>[Refactor services + controller into one](https://github.com/antoonrs/Practica_obligatoria_DAD/commit/d81fedb3a3183177726c00d77ea37b017b78c77a)</li>
        </ul>
      </td>
      <td align="left">
        <ul>
          <li>InstanceService.java</li>
          <li>DiskService.java</li>
          <li>RabbitMQConfig.java</li>
          <li>ApiService.java</li>
          <li>ApiRestController.java</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td align="left">Antón Rodríguez Seselle</td>
      <td align="left">
        <ul>
          <li>Implementación de la API REST y controladores.</li>
					<li>Implementación de las entidades</li>
          <li>Frontend del índice, y mostrar discos e instancias</li>
          <li>Creación y gestión de los endpoints web</li>
					<li>Postman Collections</li>
        </ul>
      </td>
      <td align="left">
        <ul>
          <li>"Frontend (.html templates) and basic application controller"</li>
          <li>"Disk management"</li>
          <li>"Instance management"</li>
          <li>"Update the frontend and application controller to pass data to the model…"</li>
          <li>"Commit 5"</li>
        </ul>
      </td>
      <td align="left">
        <ul>
          <li>ApiRestController.java</li>
					<li>ApiService.java</li>
          <li>Disk.java</li>
          <li>Instance.java</li>
          <li>ApplicationViewController.java</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td align="left">Bernat Roselló Muñoz</td>
      <td align="left">
        <ul>
					<li>Separación del proyecto en módulos para cada servicio y gestión de dependencias compartidas</li>
          <li>Dockerización de la aplicación en microservicios</li>
          <li>Más tareas</li>
        </ul>
      </td>
      <td align="left">
        <ul>
          <li>"Finished Maven Multi-Module refactoring"</li>
          <li>"Dockerizado los microservicios"</li>
          <li>"Commit 3"</li>
          <li>"Commit 4"</li>
          <li>"Commit 5"</li>
        </ul>
      </td>
      <td align="left">
        <ul>
          <li>docker-compose.yml</li>
          <li>file2.java</li>
          <li>file3.java</li>
          <li>file4.java</li>
          <li>file5.java</li>
        </ul>
      </td>
    </tr>
  </tbody>
</table>

## INSTRUCCIONES DE EJECUCIÓN
Desde la raíz del repositorio:
- Compilar: `docker compose build`
- Ejecutar: `docker compose up`

## POSTMAN

## DOCKER

## DOCUMENTACIÓN
### Diagrama con las entidades de las Bases de Datos
![UML Clases](https://github.com/user-attachments/assets/c1152ea4-2e7f-4f64-b94f-1dafaf0d9476)

### Diagrama de clases
![Diagrama de clases](https://github.com/user-attachments/assets/502914a9-4dbd-474e-bdfc-06e78dda637f)

### API
***ApiRestController***
- Accede a los *endpoints* de discos, instancias y la creación del servidor. Le envía el DTO ServerCreationRequest a ApiService.
```
@PostMapping("/servers")
public ResponseEntity<?> createServer(@RequestBody ServerCreationRequest request)  {
	...
	apiService.handleServerCreationRequest(request);
	...
	return ResponseEntity.accepted().body(ids);
}
```
De esta manera se puede recibir toda la información del formulario de creación de discos e instancias, y dejar que *ApiService* se encargue de enviar las peticiones de creación de discos e instancias.

***ApiService***
- Se comunica con *DiskService* e *InstanceService*, a través del Broker RabbitMQ. Envía mensajes a las colas *disk-requests* e *instance-requests* y recibe mensajes de la cola *disk-statuses* e *instance-statuses*.
- También almacena los discos e instancias en *DiskRepository* e *InstanceRepository*.

| Envío de *DiskRequest* |
| - |
```
public void sendDiskRequest(Disk disk) {
  System.out.println("Sending disk request to disk service through: " + RabbitMQConfig.diskRequestsQueueName);
  rabbitTemplate.convertAndSend(RabbitMQConfig.diskRequestsQueueName, disk);
}
```
| Recepción de *DiskStatuses* |
| - |
```
@RabbitListener(queues=RabbitMQConfig.diskStatusesQueueName, ackMode="AUTO")
public void waitForDiskStatusMessage(Disk disk) {
  ...
}
```
| Envío de *InstanceRequest* |
| - |
```
public void sendInstanceRequest(Instance instance) {
  System.out.println("Sending instance request to instance service through: " + RabbitMQConfig.instanceRequestsQueueName);
  rabbitTemplate.convertAndSend(RabbitMQConfig.instanceRequestsQueueName, instance);
}
```
| Recepción de *InstanceStatuses* |
| - |
```
@RabbitListener(queues=RabbitMQConfig.instanceStatusesQueueName, ackMode="AUTO")
public void waitForInstanceStatusMessage(Instance instance) {
  ...
}
```
***InstanceRepository***
- Interfaz que extiende JpaRepository para almacenar las instancias.
- Añade la consulta de existencia de una instancia por su Id: `Boolean existsByIP(String IP);`

***DiskRepository***
- Interfaz que extiende JpaRepository para almacenar los discos.

***ServerCreationRequest***
- Un DTO que empaqueta los discos e instancias creados desde el formulario, y los envía con AJAX al ApiRestController.

```
public class ServerCreationRequest {
	private Disk disk;
	private Instance instance;
	public ServerCreationRequest() {}
	public ServerCreationRequest(Disk disk, Instance instance, boolean hasDiskRequestEnded, boolean hasInstanceRequestEnded) {
		this.disk = disk;
		this.instance = instance;
	}
...
}
```
***TrailingSlashFilter***
- Permite redireccionar a los endpoints correctos independientemente de si se les añade "/" al final.
- Por ejemplo, si se accede a `localhost:8080/disks/` redirecciona a `localhost:8080/disks`

```
@Configuration
public class TrailingSlashFilter extends OncePerRequestFilter {
	@Override
	protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain) throws ServletException, IOException {
		...
		// Root path stays the same "/"
		if (path.length() > 1 && path.endsWith("/")) {
			String newPath = path.substring(0, path.length() - 1);
			if (query != null) {
					newPath += "?" + query;
			}
		...
		}
	}
```

### DISKS
***Disk***
- Clase que representa la entidad de discos.

| Disk | Descripción |
| - | - |
| Long id | Identificador del disco |
| int size | Tamaño del disco (GB) |
| DiskType type | Enum que puede ser *HDD* o *SSD* |
| Status status | Enum que puede ser *UNASSIGNED*, *REQUESTED*, *INITIALIZING* o *ASSIGNED* |

***DiskService***
- Espera a recibir mensajes a través de la cola *disk-requests*, y manda mensajes de cambio de estado a la cola *disk-statuses*.

| Recepción de *DiskRequest* |
| - |
```
@RabbitListener(queues=RabbitMQConfig.diskRequestsQueueName, ackMode="AUTO")
public void onDiskRequestMessage(Disk disk) {
  System.out.println("Disk request through " + RabbitMQConfig.diskRequestsQueueName + " received");
  System.out.println("Create -> " + disk);
  diskCreation(disk);
}
```
| Envío de *DiskStatuses* |
| - |
```
public void sendStatusUpdate(Disk disk, Disk.Status status) {
	disk.setStatus(status);
	System.out.println("Sending disk status updated through " + RabbitMQConfig.diskStatusesQueueName);
	rabbitTemplate.convertAndSend(RabbitMQConfig.diskStatusesQueueName, disk);
}
```
### INSTANCES
***Instance***
- Clase que representa la entidad de instancias.

| Disk | Descripción |
| - | - |
| Long id | Identificador de la instancia |
| String name | Nombre de la instancia |
| int memory | Cantidad de memoria que utiliza la instancia (GB) |
| int cores | Número de núcleos reservados para la instancia |
| String IP | Dirección IP asignada |
| Disk disk | Referencia al disco conectado |
| Status status | Enum que puede ser *BUILDING_DISK*, *STARTING*, *INITIALIZING*, *ASSIGNING_IP* o *RUNNING* |

***InstanceService***
- De manera similar a *DiskService* recibe mensajes a través de *instance-requests* y manda a través de *instance-statuses* sus cambios de estado.

| Recepción de *InstanceRequests* |
| - |
```
@RabbitListener(queues=RabbitMQConfig.instanceRequestsQueueName, ackMode="AUTO")
public void onInstanceRequestMessage(Instance instance) {
  System.out.println("Instance request through " + RabbitMQConfig.instanceRequestsQueueName + " received");
  System.out.println("Create -> " + instance);
  instanceCreation(instance);
}
```
| Envío de *InstanceStatuses* |
| - |
```
public void sendStatusUpdate(Instance instance, Instance.Status status) {
  instance.setStatus(status);
  System.out.println("Sending instance status updated through " + RabbitMQConfig.instanceStatusesQueueName);
  rabbitTemplate.convertAndSend(RabbitMQConfig.instanceStatusesQueueName, instance);
}
```

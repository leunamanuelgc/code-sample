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
          <li>Formulario para creación de discos e instancias con html y javascript</li>
          <li>Participación en la creación de entidades</li>
          <li>Participación en la API REST y en los servicios</li>
          <li>Participación en los controladores</li>
        </ul>
      </td>
      <td align="left">
        <ul>
          <li>"Added full Disk and Instance creation functionality"</li>
          <li>"Added AMQP for disks creation"</li>
          <li>"Added RabbitMQConfig, and started queueing implementation"</li>
          <li>"Add disk-instance OneToOne relation"</li>
          <li>"Refactor services + controller into one"</li>
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
          <li>Tarea</li>
          <li>Otra tarea</li>
          <li>Más tareas</li>
        </ul>
      </td>
      <td align="left">
        <ul>
          <li>"Commit 1"</li>
          <li>"Commit 2"</li>
          <li>"Commit 3"</li>
          <li>"Commit 4"</li>
          <li>"Commit 5"</li>
        </ul>
      </td>
      <td align="left">
        <ul>
          <li>file1.java</li>
          <li>file2.java</li>
          <li>file3.java</li>
          <li>file4.java</li>
          <li>file5.java</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td align="left">Bernat Roselló Muñoz</td>
      <td align="left">
        <ul>
          <li>Tarea</li>
          <li>Otra tarea</li>
          <li>Más tareas</li>
        </ul>
      </td>
      <td align="left">
        <ul>
          <li>"Commit 1"</li>
          <li>"Commit 2"</li>
          <li>"Commit 3"</li>
          <li>"Commit 4"</li>
          <li>"Commit 5"</li>
        </ul>
      </td>
      <td align="left">
        <ul>
          <li>file1.java</li>
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

## DOCUMENTACIÓN
### Diagrama con las entidades de las Bases de Datos
![UML Clases](https://github.com/user-attachments/assets/c1152ea4-2e7f-4f64-b94f-1dafaf0d9476)

### Diagrama de clases
![Diagrama de clases](https://github.com/user-attachments/assets/502914a9-4dbd-474e-bdfc-06e78dda637f)

### API
***ApiRestController***
- Accede a los *endpoints* de discos, instancias y la creación del servidor.
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
- También se comunica con *DiskRepository* e *InstanceRepository*.
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

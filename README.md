# PRÁCTICA OBLIGATORIA DESARROLLO DE APLICACIONES DISTRIBUIDAS
## Aplicación distribuida para la gestión de un centro de procesamiento de datos
Creado por:

| Alumno | Tareas realizadas | Commits | Ficheros |
| ----------- | ----------- | ----------- | ----------- |
| Manuel Gutiérrez Castro | <ul><li>Gestión de los mensajes asíncronos entre servicios</li><li>Formulario para creación de discos e instancias con html y javascript</li><li>Participación en la creación de entidades</li><li>Participación en la API REST y en los servicios</li></ul> | <ul><li>Added full Disk and Instance creation functionality</li><li>Added AMQP for disks creation</li><li>Added RabbitMQConfig, and started queueing implementation</li><li>Add disk-instance OneToOne relation</li><li>Refactor services + controller into one</li></ul> | <ul><li>InstanceService</li><li>DiskService</li><li>RabbitMQConfig</li><li>ApiService</li><li>ApiRestController</li></ul> |
| Antón Rodríguez Seselle | Text | Text | Text |
| Bernat Roselló Muñoz | Text | Text | Text |

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
        <table>
          <tbody>
            <tr><td>Gestión de los mensajes asíncronos entre servicios</td></tr>
            <tr><td>Formulario para creación de discos e instancias con html y javascript</td></tr>
            <tr><td>Participación en la creación de entidades</td></tr>
            <tr><td>Participación en la API REST y en los servicios</td></tr>
            <tr><td>Participación en los controladores</td></tr>
          </tbody>
        </table>
      </td>
      <td align="left">
        <table>
          <tbody>
            <tr><td>"Added full Disk and Instance creation functionality"</td></tr>
            <tr><td>"Added AMQP for disks creation"</td></tr>
            <tr><td>"Added RabbitMQConfig, and started queueing implementation"</td></tr>
            <tr><td>"Add disk-instance OneToOne relation"</td></tr>
            <tr><td>"Refactor services + controller into one"</td></tr>
          </tbody>
        </table>
      </td>
      <td align="left">
        <table>
          <tbody>
            <tr><td>InstanceService.java</td></tr>
            <tr><td>DiskService.java</td></tr>
            <tr><td>RabbitMQConfig.java</td></tr>
            <tr><td>ApiService.java</td></tr>
            <tr><td>ApiRestController.java</td></tr>
          </tbody>
        </table>
      </td>
    </tr>
  </tbody>
</table>

## Esquema
![DAD - Diagrama de clases](https://github.com/user-attachments/assets/83a83b99-838c-41a8-8f55-94c74e873251)

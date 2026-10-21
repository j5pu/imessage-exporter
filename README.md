# imessage-exporter

> **Fork «Revocación» (j5pu/imessage-exporter, rama `revocacion`)**
>
> Esta versión parte de la etiqueta oficial `4.3.0` de [ReagentX/imessage-exporter](https://github.com/ReagentX/imessage-exporter) (commit `4d90fc8d`) y añade solo lo necesario para extraer conversaciones de copias de seguridad de iPhone como prueba documental. El comportamiento original no cambia si no se usa la nueva opción.
>
> **Qué cambia en nuestra versión**
>
> 1. **Nueva opción `--whatsapp-format`** (solo afecta a `--format txt`). Cada mensaje sale con la misma forma que un `_chat.txt` exportado por WhatsApp:
>    - `[dd/mm/aaaa, hh:mm:ss] Remitente: texto`, hora local en formato 24 h;
>    - las líneas siguientes del mismo mensaje van sin prefijo;
>    - los mensajes propios siguen apareciendo como `Me`;
>    - sin línea en blanco entre mensajes.
> 2. **Sin acuses de lectura ni de entrega.** Con la opción activa no se escribe «(Read by … after …)». La fecha y el texto del mensaje no se tocan.
> 3. **Anexos rastreables.** Cada anexo se escribe como `<attached: ruta relativa (nombre original)>`. La ruta es relativa a la carpeta pasada con `-p` (en una copia de iPhone, por ejemplo `1f/1fda0c…`) o a la carpeta de exportación si el anexo se copió. Se escribe aunque el fichero ya no exista en la copia, para que el mensaje no quede huérfano y se pueda localizar después. Ya no aparecen rutas absolutas de carpetas temporales.
> 3b. **Citas («Responder» de iMessage).** La respuesta solo aparece una vez, en su sitio por fecha, sin la copia sangrada que la versión oficial pone bajo el mensaje citado. Debajo lleva la línea `Responde a: [dd/mm/aaaa, hh:mm:ss] Remitente` del mensaje citado, buscado en la misma base por su GUID. Si el mensaje citado no está en la copia, pone `Responde a un mensaje que no está en la copia`. Esta línea sustituye al aviso genérico «This message responded to an earlier message.».
> 3c. **Nombre de fichero.** En una conversación con una sola persona que está en la agenda, el fichero se llama `Nombre [identificador].txt`, donde el identificador es el teléfono o el email con que el iPhone guardó la conversación (por ejemplo `Arancha 1997 [+34606893792].txt`). Los demás casos conservan el nombre oficial: sin contacto, grupos, remitentes cortos y `orphaned.txt`.
> 4. **Contactos en copias de iOS 7.** La tabla `ABPersonFullTextSearch_content` de iOS 7 tiene las columnas `c15Phone` y `c16Email` (en iOS modernos son `c16Phone` y `c17Email`, porque se añadió `c7OrganizationPhonetic`). La versión oficial usaba números fijos y fallaba. La nuestra elige cada columna por su sufijo (`First`, `Last`, `Phone`, `Email`), así que sirve para las dos.
> 5. **Pruebas añadidas.** Cubren las conversiones anteriores (`whatsapp_reshape_*`, `whatsapp_get_time_has_no_read_receipt`, `ios7_fts_columns_are_found_by_suffix`, `modern_fts_columns_still_work`). Las pruebas originales siguen pasando sin cambios: 530 de 530 con `TZ=America/Los_Angeles cargo test -p imessage-exporter`. Las pruebas originales esperan horas del Pacífico, así que en otra zona horaria fallan también en la versión oficial.
>
> **Uso en el proyecto** (siempre sobre copias verificadas en `/tmp`, nunca sobre los originales):
>
> ```sh
> imessage-exporter -f txt --whatsapp-format -c disabled --no-progress \
>   -p "<copia de la copia de seguridad>" -o "<carpeta de salida>"
> ```
>
> Licencia: GPL-3.0, la misma que el proyecto original.

This crate provides both a library to interact with iMessage data as well as a binary that can perform some useful read-only operations using that data. The aim of this project is to provide the most comprehensive and accurate representation of iMessage data available.

This free and open-source software can:

- Save, export, backup, and archive iMessage data to open, portable formats
- Preserve multimedia content (images, videos, audio) from conversations
- Facilitate easy migration of message history between devices and platforms
- Run diagnostics on the iMessage database
- Give you full ownership and control over your communication history
- Support compliance with data retention policies or legal requirements
- Run on macOS, Linux, and Windows

## Example Export

![HTML Export Sample](/docs/hero.png)

## Binary

The `imessage-exporter` binary exports iMessage data to `txt` or `html` formats. It can also run diagnostics to find problems with the iMessage database.

Installation instructions for the binary are located [here](imessage-exporter/README.md).

## Library

The `imessage_database` library provides models that allow us to access iMessage information as native, cross-platform data structures.

Documentation for the library is located [here](imessage-database/README.md).

### Supported Features

This crate supports every iMessage feature as of macOS Golden Gate 27.0 and iOS 27.0:

- iMessage, RCS, SMS, and MMS
- Multi-part messages
- Replies/Threads
- Formatted text
- Attachments
- Expressives
- Tapbacks
- Stickers
- Apple Pay
- Group chats
- Digital Touch
- URL Previews
- Audio messages
- App Integrations
- Edited messages
- Business messages
- Handwritten messages

See more detail about supported features [here](docs/features.md).

## Frequently Asked Questions

The FAQ document is located [here](/docs/faq.md).

## Special Thanks

- All of my friends, for putting up with me sending them random messages to test things
- [SQLiteFlow](https://www.sqliteflow.com), the SQL viewer I used to explore and reverse engineer the iMessage database
- [Xplist](https://github.com/ic005k/Xplist), an invaluable tool for reverse engineering the `payload_data` plist format
- [Compart](https://www.compart.com/en/unicode/), an amazing resource for looking up esoteric unicode details
- [GNU Project](https://github.com/gnustep/libobjc) and [Archive.org](https://archive.org/details/darwin_0.1), for hosting source code referenced to reverse engineer the `typedstream` format

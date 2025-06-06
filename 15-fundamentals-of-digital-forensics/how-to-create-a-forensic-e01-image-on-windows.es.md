---
title: "Crear una Imagen Forense .E01 Desde Windows con FTK Imager"
description: "Guía paso a paso para adquirir evidencia digital desde discos físicos o virtuales usando FTK Imager en sistemas Windows."
tags: ["forense", "ciberseguridad", "windows", "e01", "ftk-imager"]
author: ["alesanchezr"]
---

En el análisis forense digital, una imagen `.E01` permite capturar el contenido completo de un disco duro sin alterar el original. Este archivo puede contener metadatos, fragmentarse en partes más pequeñas y verificarse automáticamente mediante hashes. A continuación, se explican dos formas de generar una imagen `.E01`: una desde un disco duro conectado físicamente al equipo, y otra desde un archivo de máquina virtual.


## Creación de imagen desde un disco duro conectado al equipo (desde Windows)

Supongamos que un disco duro fue retirado de una computadora sospechosa. El analista lo conecta a su equipo mediante un adaptador SATA-USB o una carcasa externa. El objetivo es realizar una copia bit a bit de ese disco sin modificarlo.

Para ello, se utilizará **FTK Imager**, una herramienta gratuita que permite capturar discos y generar imágenes forenses en formato `.E01`.

### Pasos:

1. Descarga e instalá `FTK Imager`.
2. Abre el programa como administrador.
3. En el menú superior, selecciona -> `File → Create Disk Image...`

    ![create-disk-image](https://github.com/rosinni/cybersecurity-syllabus/blob/main/assets/15-fundamentals-of-digital-forensics/create-disk-image.png?raw=true)

4. Elige la opción `Physical Drive`. Aparecerá una lista de discos conectados. Selecciona el disco externo (verifica que no sea tu disco del sistema) ¿Cómo lo haces? Bueno, cuando seleccionas Physical Drive en FTK Imager, vas a ver algo como:

    ```python
    PhysicalDrive0 - 500GB - WDC WD5000LPCX-...
    PhysicalDrive1 - 120GB - SanDisk SSD PLUS...
    PhysicalDrive2 - 16GB - Generic USB Flash...
    ```

    > **Observa si coincide con el tamaño de tu disco externo, el disco de tu sistema suele ser `PhysicalDrive0` por lo que no deberias seleccionarlo a menos que estés absolutamente seguro de que ese es el disco externo (lo cual casi nunca será el caso).**

5. Completa los campos del caso (por ejemplo: número de caso, nombre del perito, descripción del análisis). Pueden dejarse en blanco si es una práctica.

    ![inputs](https://github.com/rosinni/cybersecurity-syllabus/blob/main/assets/15-fundamentals-of-digital-forensics/cases-inputs.png?raw=true)

6. En tipo de imagen, seleccioná `E01 (Expert Witness Format)`.

    ![select format](https://github.com/rosinni/cybersecurity-syllabus/blob/main/assets/15-fundamentals-of-digital-forensics/format-selection.png?raw=true)

7. Elegí una carpeta de destino donde guardar la imagen.

    ![select format](https://github.com/rosinni/cybersecurity-syllabus/blob/main/assets/15-fundamentals-of-digital-forensics/select-img-destination.png?raw=true)

En opciones de configuración:
   - **Compresión**: pon el valor `6` (rápido).
   - **Fragmentación**: usa `1500 MB` para dividir la imagen en partes más manejables.



8. Presiona `Start` para comenzar la adquisición. Asegurate de activar la opción **"Verify images after they are created"**, para garantizar la integridad de la copia.

    ![select-file](https://github.com/rosinni/cybersecurity-syllabus/blob/main/assets/15-fundamentals-of-digital-forensics/start-img.png?raw=true)




Al finalizar, obtendrás una serie de archivos `.E01`, `.E02`, etc., junto con un archivo `.txt` que documenta los hashes generados y los detalles del caso.


## Creación de imagen desde una máquina virtual (archivo `.vdi` o `.vmdk`)

Cuando se trabaja con máquinas virtuales, no se dispone de un disco físico, sino de un archivo que lo representa. En VirtualBox este archivo tiene extensión `.vdi`; en VMware, `.vmdk`. El contenido de estos archivos también puede analizarse forensemente, pero primero es necesario convertirlos.

### Paso 1: Convertir el archivo de máquina virtual a `.raw`

El formato `.raw` es una copia directa del contenido del disco virtual. Para obtenerlo, se usa la herramienta `qemu-img`.

1. Descarga QEMU para Windows desde [qemu.weilnetz.de](https://qemu.weilnetz.de/w64/).
2. Extra o instala QEMU y ubica el archivo `qemu-img.exe`.
3. Abre la terminal (CMD o PowerShell) y ejecuta el comando correspondiente según tu tipo de disco:

Para VirtualBox:
```bash
qemu-img convert -f vdi -O raw "C:\ruta\al\archivo.vdi" "C:\salida\imagen.raw"
```

Para VMware:
```bash
qemu-img convert -f vmdk -O raw "C:\ruta\al\archivo.vmdk" "C:\salida\imagen.raw"
```

---

### Paso 2: Convertir `.raw` a `.E01` con FTK Imager

1. Abrí FTK Imager como administrador.
2. En el menú, seleccioná:

   `File → Create Disk Image...`

3. Seleccioná el tipo de fuente, en este caso seria `Image File`:

   ![select-source](https://github.com/rosinni/cybersecurity-syllabus/blob/main/assets/15-fundamentals-of-digital-forensics/select-source.png?raw=true)

4. Selecciona el archivo `.raw` como fuente.
    
    ![select-file](https://github.com/rosinni/cybersecurity-syllabus/blob/main/assets/15-fundamentals-of-digital-forensics/start-img.png?raw=true)

    > Asegurate de activar la opción **"Verify images after they are created"**, para garantizar la integridad de la copia.

5. Luego, haz clic en **Add** para definir el tipo de imagen de salida y donde se va guardar, selecciona:

    ![format](https://github.com/rosinni/cybersecurity-syllabus/blob/main/assets/15-fundamentals-of-digital-forensics/format-selection.png?raw=true)

6. Completa los metadatos del caso.

    ![inputs](https://github.com/rosinni/cybersecurity-syllabus/blob/main/assets/15-fundamentals-of-digital-forensics/cases-inputs.png?raw=true)

7. Elige carpeta de destino, nombre del archivo, compresión y fragmentación.

    ![select destination](https://github.com/rosinni/cybersecurity-syllabus/blob/main/assets/15-fundamentals-of-digital-forensics/select-img-destination.png?raw=true)

    > Deja desmarcada la opción “Use AD Encryption”

8. Presioná **Finish**, luego **Start** para comenzar la creación de la imagen.



## Resultado esperado

FTK Imager generará:

- Archivos segmentados: `.E01`, `.E02`, `.E03`, etc.
- Un archivo `.txt` con el hash de verificación y los metadatos del caso

    ![images results](https://github.com/rosinni/cybersecurity-syllabus/blob/main/assets/15-fundamentals-of-digital-forensics/results-e01.png?raw=true)


Estos archivos pueden analizarse directamente con herramientas como **Autopsy** o volver a cargarse en FTK Imager para explorarlos. Es importante conservar todos los archivos juntos en una misma carpeta y no modificar sus nombres si se desea mantener la integridad de la evidencia.

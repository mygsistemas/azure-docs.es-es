---
title: "Preparación de su equipo Mac y de Azure IoT Hub | Microsoft Docs"
description: Instale herramientas en su equipo Mac, cree una instancia de IoT Hub y registre su dispositivo en ella.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: desarrollo de iot, software de iot, servicio en la nube de iot, software de internet de las cosas, cli de azure, instalar python mac, instalar git en mac, ejecutar gulp, instalar node js mac
ms.assetid: 42f9d186-e20c-4ef9-98cc-71d39e058b06
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 65a25dd7a2f6a8d518217512f9e10fc9008ee728
ms.openlocfilehash: f3bed8620c13372c3af50657bb54aa8dce115529


---
# <a name="get-the-tools-macos"></a>Obtención de las herramientas (Mac OS)
> [!div class="op_single_selector"]
> * [Windows 7 o posterior](iot-hub-gateway-kit-c-sim-lesson2-get-the-tools-win32.md)
> * [Ubuntu 16.04](iot-hub-gateway-kit-c-sim-lesson2-get-the-tools-ubuntu.md)
> * [macOS 10.10](iot-hub-gateway-kit-c-sim-lesson2-get-the-tools-mac.md)

## <a name="what-you-will-do"></a>Lo que hará

- Instalación de Git, Node.js, Gulp y Python.
- Instalación de la interfaz de la línea de comandos de Azure (CLI de Azure) 

Si tiene problemas, busque soluciones en la [página de solución de problemas](iot-hub-gateway-kit-c-sim-troubleshooting.md).

## <a name="what-you-will-learn"></a>Lo qué aprenderá

En esta lección, aprenderá lo siguiente:

- Cómo instalar [Git](https://git-scm.com/) y [Node.js](https://nodejs.org/en/).
  - Git es un sistema de control de versiones distribuido de código abierto. La aplicación de ejemplo de esta lección se almacena en Git.
  - Node.js es un entorno en tiempo de ejecución de JavaScript con un amplio ecosistema de paquetes.
- Cómo usar [NPM](https://www.npmjs.com/) para instalar las herramientas de desarrollo de Node.js.
  - La versión mínima necesaria de Node.js es 4.5 LTS.
  - NPM es uno de los administradores de paquetes de Node.js.
- Cómo instalar Visual Studio Code.
  - Visual Studio Code es un editor de código fuente multiplataforma ligero pero eficaz para Windows, Linux y Mac OS. Ofrece un elevado nivel de compatibilidad para la depuración, control de Git insertado, resaltado de sintaxis, función de autocompletar de código inteligente, fragmentos de código y refactorización de código.
- Cómo instalar Python.
  - Python es un lenguaje de programación muy usado general, destinado a fines generales, interpretado y dinámico.
- Cómo instalar la CLI de Azure.
  - La CLI de Azure proporciona una experiencia de línea de comandos multiplataforma en Azure. De este modo, puede trabajar directamente desde la línea de comandos para aprovisionar y administrar los recursos.
- Cómo usar la CLI de Azure para crear una instancia de IoT Hub.

## <a name="what-you-need"></a>Lo que necesita

- Una conexión a Internet para descargar las herramientas y el software.
- Un equipo Mac con OS X Yosemite (10.10) o posterior.

## <a name="install-git-and-nodejs"></a>Instalación de Git y Node.js

Para instalar Git y Node.js, use la utilidad de administración de paquetes Homebrew siguiendo estos pasos:

1. [Descargue](http://brew.sh/) e instale Homebrew. Si ya ha instalado Homebrew, vaya al paso 2.
   1. Presione `Cmd + Space` y escriba `Terminal` para abrir una ventana de terminal.
   2. Ejecute el siguiente comando:

      ```bash
      /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
      ```

2. Instale Git y Node.js ejecutando el comando siguiente:

    ```bash
    brew install node git
    ```

## <a name="install-nodejs-development-tools"></a>Instalación de herramientas de desarrollo de Node.js

[gulp.js](http://gulpjs.com/) se usa para automatizar la implementación y ejecución de scripts.

Para instalar gulp, ejecute el comando siguiente en el terminal:

```bash
npm install -g gulp
```

Si tiene problemas con la instalación, consulte la [guía de solución de problemas](iot-hub-gateway-kit-c-sim-troubleshooting.md) para solucionar problemas habituales.

> [!Note]
> Se precisan Node, NPM y Gulp para ejecutar scripts de automatización desarrollados en Node.js.

## <a name="install-python"></a>Instalación de Python

Aunque Mac OS incluye Python 2.7 de serie, se recomienda instalar Python mediante Homebrew. Consulte [Installing Python on Mac OS X](http://docs.python-guide.org/en/latest/starting/install/osx/) (Instalación de Python en Mac OS X).

Instale Python y pip ejecutando el comando siguiente:

```bash
brew install python
```

## <a name="install-the-azure-cli"></a>Instalación de la CLI de Azure

Para instalar la CLI de Azure, siga estos pasos:

1. Ejecute los siguientes comandos en el terminal:
   ```bash
   pip install --upgrade azure-cli
   pip install --upgrade azure-cli-iot
   ```
   La instalación puede tardar 5 minutos.

2. Compruebe la instalación ejecutando el comando siguiente:
   ```bash
   az iot -h
   ```
   Si la instalación se realiza correctamente, verá el siguiente resultado.

   ![Verificación de la instalación de la CLI de Azure](media/iot-hub-gateway-kit-lessons/lesson2/az_iot_help_osx.png)

## <a name="install-visual-studio-code"></a>Instalación de Visual Studio Code

Usará Visual Studio Code más adelante en este tutorial para editar archivos de configuración.

[Descargue](https://code.visualstudio.com/docs/setup/osx) e instale Visual Studio Code.

## <a name="summary"></a>Resumen

Ha instalado todas las herramientas y el software necesarios en el equipo Mac. La siguiente tarea consiste en utilizar la CLI de Azure para crear una instancia de IoT Hub y registrar el dispositivo en ella.

## <a name="next-steps"></a>Pasos siguientes
[Creación de una instancia de IoT Hub y registro del dispositivo](iot-hub-gateway-kit-c-sim-lesson2-register-device.md)



<!--HONumber=Dec16_HO3-->



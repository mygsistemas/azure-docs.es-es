---
title: Configuración de imágenes de Azure Marketplace en Azure DevTest Labs | Microsoft Docs
description: Configuración de las imágenes de Azure Marketplace que se pueden usar al crear una máquina virtual en Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: ''

ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2016
ms.author: tarcher

---
# Configuración de imágenes de Azure Marketplace en Azure DevTest Labs
DevTest Labs admite la creación de máquinas virtuales basadas en imágenes de Azure Marketplace, en función de cómo se hayan configurado las imágenes de Azure Marketplace para usarlas en el laboratorio. En este artículo se muestra cómo especificar qué imágenes de Azure Marketplace se pueden utilizar al crear máquinas virtuales en un laboratorio, si se puede usar alguna.

## Elección de las imágenes de Azure Marketplace que se permiten al crear una máquina virtual
1. Inicie sesión en el [Portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Seleccione **Más servicios** y luego seleccione **DevTest Labs** en la lista.
3. En la lista de laboratorios, seleccione el laboratorio que desee.
4. En la hoja del laboratorio, seleccione **Configuración**.
5. En la hoja **Configuración** del laboratorio, seleccione **Marketplace images** (Imágenes de Marketplace).
6. Especifique si desea que todas las imágenes cualificadas de Azure Marketplace estén disponibles para su uso como base de una nueva máquina virtual. Si selecciona **Sí**, se permitirán en el laboratorio todas las imágenes de Azure Marketplace que cumplan los criterios siguientes:
   
   * La imagen crea una única máquina virtual.
   * La imagen se utiliza Azure Resource Manager para aprovisionar máquinas virtuales.
   * La imagen no requiere la compra de un plan de licencias adicional.
     
     Si desea que no se permitan imágenes o desea especificar las imágenes que se pueden utilizar, seleccione **No**.
     
     ![Opción para permitir que todas las imágenes de Marketplace se usen como imágenes base para las máquinas virtuales](./media/devtest-lab-configure-marketplace-images/allow-all-marketplace-images.png)
7. Si ha seleccionado **No** en el paso anterior, se habilita la casilla **Allowed images/Select all** (Imágenes permitidas/seleccionar todas). Puede utilizar esta opción junto con el cuadro de búsqueda para seleccionar o anular la selección de todos los elementos que se muestran en la lista rápidamente. También puede seleccionar individualmente las imágenes de Azure Marketplace que desea permitir para la creación de máquinas virtuales. Para ello, debe activar la casilla correspondiente a cada imagen. No seleccione nada en la lista si no desea permitir que las imágenes de Azure Marketplace se usen en el laboratorio.
   
    ![Puede especificar qué imágenes de Azure Marketplace se pueden usar como imágenes base para las máquinas virtuales](./media/devtest-lab-configure-marketplace-images/select-marketplace-images.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## Pasos siguientes
Una vez que haya configurado cómo se permiten las imágenes de Azure Marketplace al crear una máquina virtual, el siguiente paso es [agregar una máquina virtual al laboratorio](devtest-lab-add-vm-with-artifacts.md).

<!---HONumber=AcomDC_0907_2016-->
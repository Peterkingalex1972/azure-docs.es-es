---
title: archivo de inclusión
description: archivo de inclusión
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 01/16/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 98172c2c487488a72bbfdd3a8205ac7d8668db60
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035758"
---
Genere el certificado de entidad de certificación.

  ```
  ipsec pki --gen --outform pem > caKey.pem
  ipsec pki --self --in caKey.pem --dn "CN=VPN CA" --ca --outform pem > caCert.pem
  ```

Imprima el certificado de entidad de certificación en formato base64. Es el formato compatible con Azure. Más adelante lo cargará en Azure como parte de la configuración de P2S.

  ```
  openssl x509 -in caCert.pem -outform der | base64 -w0 ; echo
  ```

Genere el certificado de usuario.

  ```
  export PASSWORD="password"
  export USERNAME="client"

  ipsec pki --gen --outform pem > "${USERNAME}Key.pem"
  ipsec pki --pub --in "${USERNAME}Key.pem" | ipsec pki --issue --cacert caCert.pem --cakey caKey.pem --dn "CN=${USERNAME}" --san "${USERNAME}" --flag clientAuth --outform pem > "${USERNAME}Cert.pem"
  ```

Genere un paquete p12 que contengan el certificado de usuario. Este conjunto de productos se usará en los pasos siguientes cuando se trabaje con los archivos de configuración de cliente.

  ```
  openssl pkcs12 -in "${USERNAME}Cert.pem" -inkey "${USERNAME}Key.pem" -certfile caCert.pem -export -out "${USERNAME}.p12" -password "pass:${PASSWORD}"
  ```
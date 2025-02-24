# liveness_flutter_sdk

CSLiveness Flutter


# Necessidade de se utilizar o arquivo proguard com flag -dontwarn no Android

## Como replicar o problema?

Versão do Flutter: 3.27.2

1. Clonar este repositório
2. Instalar dependências do package e do example ```flutter pub get```
3. Comentar arquivos de testes que apontarem erro
4. Seguir processo de instalação Android (adicionar credenciais fornecidas pela Clear Sale no arquivo `clearsale.gradle.env`)
5. Rodando o App Example em debug, preenchendo as credenciais na tela de formulário, funcionará normalmente
6. Na raiz da pasta example rodar comando ```flutter build apk --no-tree-shake-icons```
7. Ao tentar gerar a APK de release, vai receber o seguinte erro:

```
ERROR: Missing classes detected while running R8. Please add the missing classes or apply additional keep rules that are generated in /Users/joao.v.santana/Desktop/forks/LivenessPluginFlutter/example/build/app/outputs/mapping/release/missing_rules.txt.

ERROR: R8: Missing class com.sun.jna.Library (referenced from: void org.xbill.DNS.config.IPHlpAPI.<clinit>() and 1 other context)
Missing class com.sun.jna.Memory (referenced from: void org.xbill.DNS.config.WindowsResolverConfigProvider$InnerWindowsResolverConfigProvider.<clinit>() and 1 other context)

Missing class com.sun.jna.Native (referenced from: void org.xbill.DNS.config.IPHlpAPI.<clinit>())
Missing class com.sun.jna.Pointer (referenced from: void org.xbill.DNS.config.IPHlpAPI$IP_ADAPTER_ADDRESSES_LH.<init>(com.sun.jna.Pointer) and 2 other contexts)
Missing class com.sun.jna.Structure$ByReference (referenced from: org.xbill.DNS.config.IPHlpAPI$IP_ADAPTER_ADDRESSES_LH$ByReference and 2 other contexts)
Missing class com.sun.jna.Structure$FieldOrder (referenced from: org.xbill.DNS.config.IPHlpAPI$IP_ADAPTER_ADDRESSES_LH and 3 other contexts)
Missing class com.sun.jna.Structure (referenced from: void org.xbill.DNS.config.IPHlpAPI$IP_ADAPTER_ADDRESSES_LH.<init>(com.sun.jna.Pointer) and 4 other contexts)
Missing class com.sun.jna.WString (referenced from: com.sun.jna.WString org.xbill.DNS.config.IPHlpAPI$IP_ADAPTER_ADDRESSES_LH.DnsSuffix and 1 other context)
Missing class com.sun.jna.platform.win32.Win32Exception (referenced from: void org.xbill.DNS.config.WindowsResolverConfigProvider$InnerWindowsResolverConfigProvider.<clinit>() and 1 other context)
Missing class com.sun.jna.ptr.IntByReference (referenced from: int org.xbill.DNS.config.IPHlpAPI.GetAdaptersAddresses(int, int, com.sun.jna.Pointer, com.sun.jna.Pointer, com.sun.jna.ptr.IntByReference) and 1 other context)
Missing class com.sun.jna.win32.W32APIOptions (referenced from: void org.xbill.DNS.config.IPHlpAPI.<clinit>())
Missing class javax.naming.NamingException (referenced from: void org.xbill.DNS.config.JndiContextResolverConfigProvider$InnerJndiContextResolverConfigProvider.initialize())
Missing class javax.naming.directory.DirContext (referenced from: void org.xbill.DNS.config.JndiContextResolverConfigProvider$InnerJndiContextResolverConfigProvider.<clinit>() and 1 other context)
Missing class javax.naming.directory.InitialDirContext (referenced from: void org.xbill.DNS.config.JndiContextResolverConfigProvider$InnerJndiContextResolverConfigProvider.initialize())
Missing class lombok.Generated (referenced from: org.slf4j.Logger org.xbill.DNS.Cache.log and 31 other contexts)
Missing class org.slf4j.impl.StaticLoggerBinder (referenced from: void org.slf4j.LoggerFactory.bind() and 3 other contexts)
Missing class sun.net.spi.nameservice.NameServiceDescriptor (referenced from: org.xbill.DNS.spi.DNSJavaNameServiceDescriptor)
```


6. Adicionar o arquivo ```proguard-rules.pro``` com o conteúdo do ```missing_rules.txt``` na pasta ```example/android/app``` e rodar o ```flutter build apk --no-tree-shake-icons``` novamente.
7. A APK de release será gerada normalmente.


Temos preocupações sobre utilizar o ```-dontwarn``` para ignorar os erros encontrados no processo de minificação da APK. 
O uso dessa flag pode implicar no mal comportamento em funcionalidades que façam o uso dessas classes. Pode impactar o processo de segurança da APK e além disso não é a prática mais recomendada pela documentação.


obs: A página [guia de solução de problemas do ProGuard](https://www.guardsquare.com/en/products/proguard/manual/troubleshooting) alerta:

```
Warning: can't find superclass or interface
Warning: can't find referenced class

A class in one of your program jars or library jars is referring to a class or interface that is missing from the input. The warning lists both the referencing class(es) and the missing referenced class(es). There can be a few reasons, with their own solutions:

If the missing class is referenced from a pre-compiled third-party library, and your original code runs fine without it, then the missing dependency doesn't seem to hurt.

The cleanest solution is to filter out the referencing class or classes from the input, with a filter like "-injars myapplication.jar(!somepackage/SomeUnusedReferencingClass.class)". 

DexGuard will then skip this class entirely in the input, and it will not bump into the problem of its missing reference. 

However, you may then have to filter out other classes that are in turn referencing the removed class. 

In practice, this works best if you can filter out entire unused packages at once, with a wildcard filter like "-libraryjars mylibrary.jar(!someunusedpackage/**)".


If you don't feel like filtering out the problematic classes, you can try your luck with the -ignorewarnings option, or even the -dontwarn option. Only use these options if you really know what you're doing though.
```

- Atenção aqui: “you can try you own luck” para o -dontwarn

```
Warning: can't find referenced field/method '...' in library class ...

A program class is referring to a field or a method that is missing from a library class. The warning lists both the referencing class and the missing referenced class member. Your compiled class files are inconsistent with the libraries. You may need to recompile the class files, or otherwise upgrade the libraries to consistent versions.

If there are unresolved references to class members in program classes, your compiled class files are most likely inconsistent. Possibly, some class file didn't get recompiled properly, or some class file was left behind after its source file was removed. Try removing all compiled class files and rebuilding your project.

If there are unresolved references to class members in library classes, your compiled class files are inconsistent with the libraries. You may need to recompile the class files, or otherwise upgrade the libraries to consistent versions.

Alternatively, you may get away with ignoring the inconsistency with the options -ignorewarnings or even -dontwarn.
```

## Instalação

```sh
flutter pub add liveness_flutter_sdk
```

#### Android
Adicione um arquivo `clearsale.gradle.env` na raiz do seu projeto flutter.
Esse arquivo deve conter as seguintes propriedades:

```
CS_LIVENESS_TEC_ARTIFACTS_FEED_URL=ARTIFACTS_FEED_URL // valor fornecido pela clear sale
CS_LIVENESS_TEC_ARTIFACTS_FEED_NAME=ARTIFACTS_FEED_NAME // valor fornecido pela clear sale
CS_LIVENESS_TEC_USER=USERNAME // valor fornecido pela clear sale
CS_LIVENESS_TEC_PASS=ACCESSTOKEN // valor fornecido pela clear sale
CS_LIVENESS_VERSION=LAST_VERSION // valor fornecido pela clear sale
```

### iOS
No arquivo `Podfile` de seu projeto adicione o seguinte código:

```
platform :ios, '12.0'

use_frameworks!

target 'NOME_DO_SEU_PROJETO' do
  pod 'CSLivenessSDK', :git => 'https://CS-Packages:YOUR_PAT_HERE@dev.azure.com/CS-Package/ID-Lab-PackagesSDK/_git/CSLivenessSDK', :tag => 'VERSION_HERE'
end
```

## Instruções de uso
Importe o plugin no seu projeto e use o método `openCSLiveness` que irá chamar a SDK nativa do dispositivo.

O resultado da função `open` é um `future` que pode retornar os seguintes valores:
```dart
class CSLivenessResult {
  final bool real;
  final String sessionId;
  final String image;
  final String? responseMessage;
}
```

Caso o `future` seja rejeitado, ela retorna o erro que causou a rejeição (`CSLivenessResult no Android`, `CSLivenessError no iOS` e `UserCancel`).

## Exemplo de uso
```dart
// Platform messages are asynchronous, so we initialize in an async method.
Future<void> callCSLivenessSDK(
    String accessToken,
    String transactionId,
    bool vocalGuidance,
    Color primaryColor,
    Color secondaryColor,
    Color titleColor,
    Color paragraphColor) async {
  // Platform messages may fail, so we use a try/catch PlatformException.
  // We also handle the message potentially returning null.
  String result;
  try {
    var sdkResponse =
    await _livenessFlutterSdkPlugin.openCSLiveness(
        accessToken,
        transactionId,
        vocalGuidance,
        primaryColor,
        secondaryColor,
        titleColor,
        paragraphColor);

    result = jsonEncode(sdkResponse);
  } on PlatformException catch (e) {
    result = "Error: $e";
  } catch (e) {
    result = "Error: $e";
  }

  // If the widget was removed from the tree while the asynchronous platform
  // message was in flight, we want to discard the reply rather than calling
  // setState to update our non-existent appearance.
  if (!mounted) return;

  setState(() {
    _result = result;
  });
}
```

### Como obter o accessToken e transactionId?
- `accessToken`: Faça a autenticação seguindo as instruções da [API DataTrust](https://devs.plataformadatatrust.clearsale.com.br/reference/post_v1-authentication) e obtenha o `token` do retorno.
- `transactionId`: Crie uma transação seguindo as instruções da [API DataTrust](https://devs.plataformadatatrust.clearsale.com.br/reference/post_v1-transaction) e obtenha o `id` do retorno.


## Executando o aplicativo de exemplo

1. Conecte um dispositivo físico (`Android` ou `iOS` - o nosso `SDK` não roda em emuladores, apenas em dispositivos fisícos) à sua máquina de desenvolvimento.
2. Clone esse repositório e rode `flutter pub get`.
3. Coloque suas credenciais no arquivo `clearsale.gradle.env` na raiz do projeto de exemplo e adicione também as credenciais no arquivo `example/ios/Podfile`.
4. Inicie o app
5. Ao pressionar o botão `Open CSLiveness` o SDK Liveness iniciará. Após completar o fluxo o aplicativo retorna o resultado.

## Detalhes de privacidade

**Uso de dados**

Todas as informações coletadas pelo SDK da ClearSale são com exclusiva finalidade de prevenção à fraude e proteção ao próprio usuário, aderente à política de segurança e privacidade das plataformas Google e Apple e à LGPD. Por isso, estas informações devem constar na política de privacidade do aplicativo.

**Tipo de dados coletados**

O SDK da ClearSale coleta as seguintes informações do dispositivo :

Características físicas do dispositivo/ hardware (Como tela, modelo, nome do dispositivo);
Características de software (Como versão, idioma, build, controle parental);
Informações da câmera;
Licença de Uso
Ao realizar o download e utilizar nosso SDK você estará concordando com a seguinte licença:

**Copyright © 2022 ClearSale**

Todos os direitos são reservados, sendo concedida a permissão para usar o software da maneira como está, não sendo permitido qualquer modificação ou cópia para qualquer fim. O Software é licenciado com suas atuais configurações “tal como está” e sem garantia de qualquer espécie, nem expressa e nem implícita, incluindo mas não se limitando, garantias de comercialização, adequação para fins particulares e não violação de direitos patenteados. Em nenhuma hipótese os titulares dos Direitos Autorais podem ser responsabilizados por danos, perdas, causas de ação, quer seja por contrato ou ato ilícito, ou outra ação tortuosa advinda do uso do Software ou outras ações relacionadas com este Software sem prévia autorização escrita do detentor dos direitos autorais.

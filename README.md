# # App Checklist - Gestão de Patrimônio

## Visão Geral
O **App Checklist** é um aplicativo multiplataforma (iOS e Android) desenvolvido em **Flutter**, voltado para gestão de patrimônio. Ele permite que gestores, professores e equipes administrativas localizem e monitorem patrimônios em tempo real.

## Tecnologias Utilizadas
- **Frontend (App Móvel):** Flutter
- **Banco de Dados:** Firebase Firestore (sem servidor) e PostgreSQL
- **Autenticação:** Firebase Authentication (e-mail/senha, Google, Microsoft OAuth)
- **QR Code:** Leitura e geração de códigos QR (flutter_qr_code, qr_code_scanner)
- **Notificações:** Firebase Cloud Messaging (FCM) ou Twilio
- **Backend:** Firebase Firestore para armazenamento em tempo real e PostgreSQL para histórico de longo prazo
- **Painel Web (Opcional):** React para gestão e relatórios

## Funcionalidades Principais
### 1. Consulta de Patrimônio
- **Busca manual:** Digitar o número do patrimônio para visualizar localização atual e histórico de movimentação.
- **Busca por QR Code:** Escanear o QR Code para obter informações detalhadas do item, incluindo status e última atualização.

### 2. Controle de Entrada/Saída
- Cadastro de novos patrimônios (número, descrição, localização inicial).
- Registro de saída para manutenção, informando motivo, destino e status.

### 3. Relatórios e Sincronização
- **Histórico de movimentações**, filtrado por escola, tipo de equipamento e status.
- **Sincronização offline:** Dados registrados offline são enviados ao servidor quando houver conexão.

### 4. Notificações Automatizadas
- Alerta quando um equipamento for marcado como "Precisa de Manutenção".
- Atualização em tempo real dos status dos itens.

## Estrutura do Banco de Dados (Firebase Firestore)
```json
"equipamentos": {
  "patrimonio_id_00000525": {
    "nome": "Notebook Dell",
    "status": "Funcionando",
    "ultima_verificacao": "2025-03-19",
    "responsavel": "Usuário X"
  },
  "epatrimonio_id_000005299": {
    "nome": "Arduino Mega",
    "status": "Precisa de Manutenção"
  }
}
```

## Configuração do Projeto
1. **Configurar o ambiente Flutter**
```yaml
dependencies:
  flutter:
    sdk: flutter
  firebase_core: latest
  firebase_auth: latest
  cloud_firestore: latest
  qr_code_scanner: latest
  provider: latest
  http: latest
  dio: latest
  postgres: latest
```
2. **Criar projeto no Firebase** e configurar Firestore, Authentication e Cloud Functions.

## Implementação
### 1. Autenticação com Firebase
```dart
Future<User?> signInWithEmail(String email, String password) async {
  try {
    UserCredential userCredential = await FirebaseAuth.instance.signInWithEmailAndPassword(
      email: email,
      password: password,
    );
    return userCredential.user;
  } catch (e) {
    print('Erro ao autenticar: $e');
    return null;
  }
}
```

### 2. Leitura de QR Code
```dart
QRViewController? controller;
@override
Widget build(BuildContext context) {
  return QRView(
    key: GlobalKey(),
    onQRViewCreated: (QRViewController controller) {
      this.controller = controller;
      controller.scannedDataStream.listen((scanData) {
        print('QR Code lido: ${scanData.code}');
      });
    },
  );
}
```

### 3. Sincronização com PostgreSQL
```dart
import 'package:postgres/postgres.dart';
Future<void> inserirVerificacaoNoPostgreSQL(String equipamentoId, String status, String usuario) async {
  final connection = PostgreSQLConnection(
    'seu-host.postgres.database.azure.com',
    5432,
    'seu_banco',
    username: 'seu_usuario',
    password: 'sua_senha',
  );
  await connection.open();
  await connection.query(
    'INSERT INTO verificacoes (equipamento_id, status, usuario, data) VALUES (@equipamentoId, @status, @usuario, NOW())',
    substitutionValues: {
      'equipamentoId': equipamentoId,
      'status': status,
      'usuario': usuario,
    },
  );
  await connection.close();
}
```

## Diferenciais
✅ **Offline-first:** Funciona sem internet e sincroniza depois.  
✅ **Notificações push:** Alertas automáticos sobre status dos equipamentos.  
✅ **Dashboard Web:** Painel em React para gestão de patrimônios.  
✅ **Relatórios avançados:** Análise de falhas frequentes e status dos itens.  

## Contribuição
1. Faça um **fork** do repositório.
2. Crie uma **branch** para sua funcionalidade (`git checkout -b feature-nova`).
3. Envie um **pull request** quando terminar.

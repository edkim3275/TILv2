# WALLET 🔐

## Self Q&A

### 통합지갑 개발에 대한 고찰

- 현재 개발하려는 블록체인 월렛은 HD(Hierarchical Deterministic) wallet

- HD 지갑은 여러 개의 주소를 관리하고, 각 주소는 경로와 인덱스를 통해 유도됨

- 따라서 여타 다른 네트워크에서 생성한 지갑을 가져오기 위해서는 해당 지갑의 개인키, 경로와 인덱스가 필요

- 일반적인 경로는 아래와 같은 형식을 가짐

  `m / purpose' / coin_type' / account' / change / address_index`

  ex) `m/44'/60'/0'/0/0`

  `m`은 마스터 키를 나타내고, `44'`는 BIP-44의 경로 등을 나타냄. 다른 경로 정보도 있을 수 있고, 각 경로는 다른 주소를 생성함

- 메타마스크 TEST

  > 그렇다면 메타마스크는 어떠한 경로로 지갑을 생성하고 있을까

  - 일단 메타마스크는 계정마다 개인키가 모두 다르다는 것을 알 수 있었음(생성된 계정마다 고유의 개인키 부여)

  - 즉, 메타마스크 지갑에 대한 마스터키(복구구문)가 존재하고 해당 마스터키로 파생되는 개인키가 존재

  - 계정추가로직

    마스터키로 생성된 지갑의 경로로부터 인덱스가 1씩 더해지면서 생성이됨

    ```javascript
    const { ethers } = require('ethers');
    
    const recoverPhrase = '12개 혹은 24개의 복구구문(단어)';
    
    // first wallet from masterkey
    const masterWallet = ethers.HDNodeWallet.fromPhrase(recoverPhrase);
    // second wallet from privatekey
    const childWallet = ethers.HDNodeWallet.fromMnemonic(recoverPhrase, "m/44'/60'/0'/0/1")
    ```

- 남아있는 궁금증 및 개선점

  - 가져온 지갑과 기존 지갑과의 효과적인 구분 방법은 무엇일지
  - (부리또 월렛) 멀티 코인 지갑 vs. 체인별 지갑

  


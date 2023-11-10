# WALLET 🔐

## Dev History

### Web3.js vs. Ethers.js

> Wallet FE 개발을 하게되면 자연스럽게 접해봤을 두 라이브러리로 web3.js와 ethers.js가 있음. 다만 두 가지 라이브러리의 특징이나 차이에 대해 알고 사용하자.

- 두 가지 모두 decentralized applications 빌딩을 위한 라이브러리
- 스마트 컨트랙트와 상호작용하고 EVM 호환 블록체인에서 트랜잭션을 처리하는 다양한 방법을 제공하는 노드 패키지
- web3.js는 이더리움 네트워크 기반이고, 스마트 컨트랙트 개발을 위한 라이브러리라고 한다면 ethers.js 디지털 자산과 스마트 컨트랙트 생성을 위한 라이브러리라고 볼 수 있음
- web3.js
  - Ethereum Foundation에서 개발하고 유지보수하는 공식 JavaScript API 라이브러리
  - 주로 이더리움 블록체인과 상호 작용하는 DApp 개발에 사용
- ethers.js
  - Richard Moore(https://github.com/ricmoo)가 개발
  - 이더리움 블록체인 및 해당 생태계와 상호 작용하기 위한 라이브러리
  - API를 두 가지 역할로 나누어 개발자에게 유연성을 제공
    - 이더리움 네트워크에 대한 익명 연결기능을 제공하는 공급자(provider)
    - 개인키에 엑세스하고 거래에 서명하는 기능을 제공하는 서명자(signer)
- 결국 프로젝트의 요구사항, 개발자 경험, 커뮤니티 지원 등 요인에 의해 결정하면 됨

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


### 멀티 코인 지갑

- 로똔다에서 개발하고있는 브리또 월렛에서는 다양한 네트워크에서 사용되는 코인을 통합 관리할 수 있는 멀티 코인 지갑을 제공

- 어떠한 방식으로 해당 서비스를 제공하는걸까 생각해보면

  - 처음 회원가입 시(휴대폰 본인인증, 로그인 비밀번호 등의 정보입력) 마스터키를 발급하고 해당 키를 디바이스에 저장
  - 마스터키에 대한 디바이스 저장의 경우
    - 모바일 디바이스의 경우 안전한 저장소나 키체인에 암호화된 개인키를 저장
    - 안전한 저장소는 iOS 기기의 경우 Secure Enclave이나 Android 기기의 경우 Strongbox를 사용할 수 있음
    - 대부분의 모바일 운영체제에서 개인키를 안전하게 저장하기 위해 키체인을 활용. 키체인은 개발자가 접근할 수 없고 안전한 암호화를 제공
    - 안드로이드의 경우 Android Keystore라고, 안드로이드 운영체제에서 개인키를 안전하게 저장하기 위한 시스템 수준의 저장소를 제공
  - 멀티 코인 지갑은 마스터키로 네트워크에 따른 공개키 및 주소를 생성하여 제공하는 네트워크의 지갑을 생성(하나의 멀티 코인 지갑은 제공하는 네트워크 종류에 대한 코인 관리가 가능)
  - 저장된 마스터키를 사용하여 추가 멀티 코인 지갑을 생성
  - 지갑 가져오기의 경우
    - 가져오려는 지갑의 네트워크를 선택 후, 해당 지갑의 개인키를 입력하므로 사전에 업무 협약이 맺어져 있거나, 로똔다 측에서 파악된 지갑 경로를 통해 해당 지갑을 재생성하는 것이 가능하지 않을까 추측됨

- 그렇다면 웹에서 제공하는 블록체인 지갑의 경우 개인키를 어디에 보관할 수 있을까

  - 로컬 스토리지에 암호화된 키스토어를 저장할 수 있으나 브라우저의 취약점이나 보안 문제로 위험할 수 있음

    ```javascript
    // 개인키 저장 함수
    function storePrivateKey(encryptedPrivateKey) {
      // 로컬 스토리지에 암호화된 개인키 저장
      localStorage.setItem('encryptedPrivateKey', encryptedPrivateKey);
    }
    ```

  - 브라우저 확장 프로그램에서 개인키를 안전하게 관리하고 암호화할 수 있음

    - 브라우저 확장 프로그램이 지원하는 안전한 스토리지나 브라우저의 확장 스토리지를 사용하면 아래와 같이 로직을 구성할 수 있을 것 같다.

      ```javascript
      // 브라우저 확장 프로그램의 안전한 스토리지 사용
      const storage = chrome.storage.local; // Chrome 확장 프로그램 예제, Firefox와 같은 다른 브라우저는 다른 스토리지 API를 사용할 수 있음
      
      // 개인키 저장
      const storePrivateKey = (encryptedPrivateKey) => {
        const data = { privateKey: encryptedPrivateKey };
        
        storage.set(data, () => {
          if (chrome.runtime.lastError) {
            console.error('Error storing private key: ' + chrome.runtime.lastError);
          } else {
            console.log('Private key stored successfully');
          }
        })
      }
      
      // 개인키 로드
      const loadPrivateKey = (callback) => {
        storage.get(['privateKey'], (result) => {
          const encryptedPrivateKey = result.privateKey || null;
          callback(encryptedPrivateKey);
        })
      }
      
      // 개인키 로드 및 복호화
      loadPrivateKey(function(encryptedPrivateKey) {
        if (encryptedPrivateKey) {
          const decryptedPrivateKey = decrypt(encryptedPrivateKey, password);
          console.log('Loaded and decrypted Private Key:', decryptedPrivateKey);
        } else {
          console.error('Private Key not found.');
        }
      });
      ```

    - 일부 브라우저 확장 프로그램은 암호 관리와 관련된 추가 보안 기능을 제공할 수 있음. 예를 들어, 안전한 비밀번호 생성, 2단계 인증, 생체 인식 기술 등을 통해 더 높은 보안 수준을 제공하는 경우가 있습니다.

  - 개인키를 하드웨어 지갑에 저장하여 트랜잭션을 서명하거나 기타 작업을 수행할 때 하드웨어 지갑을 연결하여 개인키를 활용할 수 있음

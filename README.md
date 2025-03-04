
## **Paytaca Wallet: Multisignature (Multisig) Support with PSBT Implementation Specification**

### **1. Objective:**
Implement multisignature (multisig) functionality into the Paytaca Wallet, using Partially Signed Bitcoin Transactions (PSBTs). This implementation will allow multiple signers to sign a transaction, enhancing security, flexibility.

### **2. Background:**
Multisignature wallets require multiple private keys to authorize a transaction. PSBT is an open standard that allows users to create, sign, and broadcast partially signed transactions, which is highly suitable for multisig wallets. Bitcoin Cash, using a P2SH address format, will allow multisig transactions to be created and signed using PSBT.

### **3. Features:**

#### **3.1. Create Multisig Wallet:**
- **Input:** User specifies the number of total signers (M) and the required signers (N).
  - Example: 2-of-3 multisig (M=2, N=3) means two of the three signers are required to sign a transaction.
- **Output:** A P2SH address generated based on the provided M and N values, utilizing the public keys of the participants.
- The wallet will generate and store the public keys for the participants and will allow the import/export of these keys in the form of PSBT or raw public keys.

#### **3.2. Transaction Creation (PSBT Format):**
- **Input:** The wallet generates a PSBT that includes the following information:
  - Inputs: The UTXOs that are being spent.
  - Outputs: The recipient addresses and amounts.
  - Multisig Script: The script associated with the P2SH address (in the form of `M of N`).
- **Output:** The wallet will generate a PSBT file, which can be saved, shared, and signed by other participants.

#### **3.3. Transaction Signing with PSBT:**
- **Input:** Each signer can load the PSBT into the wallet and provide their signature using their private key.
- **Output:** Each signer adds their signature to the PSBT, creating a **partially signed transaction**. The PSBT will remain in a partially signed state until the required number of signatures is reached.
  - Once a signer has added their signature, the wallet will mark the PSBT as “signed by [signer].”
  - The process can be paused, resumed, and signatures can be added in any order.

#### **3.4. Transaction Broadcasting:**
  - Once the PSBT has the required number of signatures, it will be finalized and broadcasted to the Bitcoin Cash network. 
  - The wallet will combine all signatures into the PSBT and broadcast it as a fully signed transaction.
  - If any signatures are missing or invalid, the transaction will be rejected.

#### **3.5. Key Management (PSBT & Multisig):**
- The wallet stores the public keys of all participants in the multisig setup but never the private keys.
- Each signer must have access to their private key for signing.
- When a signer signs the PSBT, the private key remains confidential and is not exposed or transmitted over the network.

#### **3.6. Multisig Address Format:**
  - The wallet should generate P2SH addresses for the multisig setup using the multisig script format, which will be used in the PSBT.
  - Example format: `bitcoincash:pq97ds60uzsgk4n68spqt9zfxr4r27pu4s02hz74xw`

#### **3.7. UI/UX for PSBT and Multisig:**
- Provide a user-friendly interface that allows the user to:
  - Create a new multisig wallet and set the number of required signers and total signers.
  - Import public keys for each signer.
  - Generate a PSBT and share it with signers.
  - Load the PSBT and manage the signing workflow.
  - The UI should handle all PSBT-specific actions transparently to the user, providing clear feedback on the transaction status and signing progress.

#### **3.8. Security:**
- The private keys of all signers must remain confidential and must not be exposed in the wallet.
- Implement features such as timeouts or expiration dates for PSBT transactions to prevent them from lingering indefinitely.

### **4. Technical Requirements:**

#### **4.1. PSBT Integration:**
- Implement PSBT (BIP174) functionality within the wallet, supporting the full lifecycle of a PSBT:
  - **Creation:** Generate PSBT with multisig script and necessary inputs/outputs.
  - **Signing:** Add signatures from multiple signers, ensuring that each signature is added properly without overriding others.
  - **Finalization:** Combine all signatures and finalize the PSBT into a fully signed transaction.
- Ensure that the wallet can import, export, and share PSBTs.

#### **4.2. Address Generation:**
  - The wallet must generate P2SH addresses (multisig addresses) based on the public keys provided, with the script being of the form:
  - `M <public_key_1> <public_key_2> ... <public_key_n> N OP_CHECKMULTISIG`
  - The wallet will encode this script into a P2SH address and return it to the user.

#### **4.3. Transaction Signing Process (PSBT):**
- When a user selects to sign a transaction, the wallet should load the PSBT and allow the signer to sign it using their private key.
- Once a signature is added, the PSBT should be updated to include the new signature.
- The wallet should support verifying that the PSBT has the correct number of signatures before it is broadcast.

#### **4.4. Error Handling and Transaction Validation:**
- Properly handle errors such as missing signatures, invalid signatures, or network issues.
- Notify the user when the required signatures have been collected and when the transaction is ready for broadcasting.
- Display the signing status in the UI and give clear instructions on what actions need to be taken by each signer.

#### **4.5. PSBT Security:**
- Implement secure encryption for PSBT files when stored locally or shared between signers.
- Ensure that partial PSBTs can be signed without revealing sensitive data, such as private keys or unspent transaction outputs (UTXOs).

#### **4.6. Testing & Validation:**
  - Conduct comprehensive testing to ensure PSBT handling works seamlessly with multisig transactions:
  - Test the full process of creating, signing, and broadcasting PSBT transactions.
  - Ensure that all signers can sign in any order and the transaction remains valid.
  - Validate that the wallet correctly handles errors and edge cases (e.g., insufficient signatures, invalid scripts).

### **5. User Flow Example:**

1. **Create Multisig Wallet:**
   - User selects "Create Multisig Wallet" from the wallet interface.
   - User specifies the number of required signers (e.g., 2 out of 3).
   - User adds the public keys of the other signers (either manually or by scanning QR codes).
   - Wallet generates a multisig P2SH address and displays it to the user.

2. **Transaction Creation and PSBT Generation:**
   - User creates a transaction or receive an unsigned transaction from a DAPP.
   - The wallet generates a PSBT, including the necessary inputs, outputs, and the multisig script.
   - The PSBT can be copied or downloaded and shared with other signers.

3. **Transaction Signing:**
   - Each signer signs the PSBT with their private key.
   - Once all required signatures are collected, the PSBT is finalized.

4. **Transaction Broadcasting:**
   - The wallet may or may not(e.g. if a DAPP created the transaction) broadcasts the fully signed PSBT to the Bitcoin Cash network.

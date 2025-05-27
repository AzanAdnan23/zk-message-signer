

# Circom Circuit 


## 1. Compile Circom Circuit

```bash
circom "$CIRCOM_FILE" -l "$LIB_DIR" --r1cs --wasm --output "$TARGET_DIR"
```

* **`$CIRCOM_FILE`**: Path to your `.circom` file
* **`$LIB_DIR`**: Directory containing external circuit dependencies
* **`$TARGET_DIR`**: Output directory for compiled artifacts (`.r1cs`, `.wasm`, etc.)

---

## 2. Trusted Setup

### Phase 1: Powers of Tau Ceremony (Universal Setup)

```bash
snarkjs powersoftau new bn128 12 pot12_0000.ptau -v
snarkjs powersoftau contribute pot12_0000.ptau pot12_0001.ptau --name="Foo" -v
```

* Initializes and contributes to the Powers of Tau ceremony
* You may use your own name or identifier for contributions

---

### Phase 2: Circuit-Specific Setup

```bash
snarkjs powersoftau prepare phase2 "$PROJECT_DIR/$PTAU_FILE" pot12_final.ptau -v
```

#### Generate zKey:

```bash
snarkjs groth16 setup "${R1CS_BASENAME}.r1cs" pot12_final.ptau "${R1CS_BASENAME}_0000.zkey"
```

#### Contribute to zKey:

```bash
snarkjs zkey contribute "${R1CS_BASENAME}_0000.zkey" "${R1CS_BASENAME}_0001.zkey" --name="Foo" -v
```

#### Export Verification Key:

```bash
snarkjs zkey export verificationkey "${R1CS_BASENAME}_0001.zkey" "${R1CS_BASENAME}_verification_key.json"
```

---

## 3. Generate Proof

```bash
snarkjs groth16 prove $ZKEY_PATH $WITNESS_PATH $TARGET_DIR/proof.json $TARGET_DIR/public.json
```

* **`$ZKEY_PATH`**: Final zkey file
* **`$WITNESS_PATH`**: Generated witness `.wtns` file
* Outputs the `proof.json` and `public.json` to the target directory

---

## 4. Verify Proof

```bash
snarkjs groth16 verify $VERIFICATION_KEY $PUBLIC_OUTPUT $PROOF_FILE
```

* **`$VERIFICATION_KEY`**: Path to the verification key JSON
* **`$PUBLIC_OUTPUT`**: `public.json` file
* **`$PROOF_FILE`**: `proof.json` file


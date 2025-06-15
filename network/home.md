Bom dia, Rodrigo! Entendi que você quer acessar os notebooks da sua rede doméstica usando **nomes (ex: `meu-notebook.local`)** em vez de IPs. Isso é totalmente possível, mas o roteador TP-Link Archer AX53 **não oferece um servidor DNS local** com resolução automática de nomes por padrão. No entanto, você tem algumas opções:

---

## ✅ **Opção 1: Usar mDNS / Bonjour (Recomendado se for simples e só dentro de casa)**

Se os notebooks são Windows, macOS ou Linux e estão na mesma rede, **você pode usar mDNS**, que permite resolver nomes locais como:

```
meu-notebook.local
```

### O que você precisa:

* Nos **Macs e Linux**, isso funciona automaticamente (já usam `avahi` ou `Bonjour`).
* No **Windows**, é necessário instalar o **Bonjour** (vem com iTunes ou você pode instalar separado: [Bonjour Print Services da Apple](https://support.apple.com/kb/dl999?locale=pt_BR)).

### Como testar:

No terminal de outro computador, tente:

```bash
ping nome-do-notebook.local
```

Se funcionar, ótimo! Agora você pode acessar via nome.

---

## ✅ **Opção 2: Configurar os nomes manualmente via `hosts`**

Se os IPs dos notebooks forem estáticos ou reservados no roteador, você pode editar o arquivo `hosts` em cada notebook para associar nomes aos IPs:

### Exemplo (`hosts` no Windows ou Linux/macOS):

```text
192.168.0.105   notebook-do-rodrigo
192.168.0.106   notebook-da-esposa
```

**No Windows**, o arquivo fica em:

```
C:\Windows\System32\drivers\etc\hosts
```

(É necessário abrir o bloco de notas como administrador para editá-lo)

**No Linux/macOS**, fica em:

```
/etc/hosts
```

---

## ✅ **Opção 3: Usar um Raspberry Pi ou notebook como servidor DNS local**

Se você quer uma solução mais robusta e automática:

* Instale o **dnsmasq** em um Raspberry Pi ou notebook sempre ligado.
* Ele pode atuar como DNS local, resolvendo os nomes de forma centralizada com base no DHCP.

Essa opção é mais técnica, mas dá controle total. Posso te orientar se quiser seguir por esse caminho.

---

## ❌ O que não funciona com seu roteador:

O Archer AX53 **não faz resolução de nomes locais automaticamente** via DNS. Ele fornece IPs via DHCP, mas **não registra nomes** para você buscar por `nomedohost` diretamente, a menos que um serviço externo (como o mDNS ou um DNS local) esteja cuidando disso.

---

## Resumo das recomendações:

| Solução                    | Nível técnico | Recomendado para      |
| -------------------------- | ------------- | --------------------- |
| mDNS / `.local`            | Fácil         | Uso doméstico simples |
| Editar arquivo `hosts`     | Médio         | Pequenas redes fixas  |
| Usar DNS local com dnsmasq | Avançado      | Redes maiores/fixas   |

---

Se quiser ajuda para alguma dessas opções (instalar Bonjour, configurar o `hosts`, ou montar um mini servidor DNS), me avise que te guio passo a passo!

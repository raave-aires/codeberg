# Como espelhar seus repositórios do GitHub no Codeberg
Espelhar um repositório do GitHub para o Codeberg pode ser uma ótima solução para quem busca maior independência e privacidade no desenvolvimento de projetos.

### Por que espelhar seus repositórios?
Existem várias razões para você querer espelhar seu repositório no Codeberg. Por exemplo:
- **Privacidade.** Você pode evitar que seus usuários interajam diretamente com servidores de grandes corporações, como os da Microsoft.
- **Distribuição.** Manter seu repositório no GitHub garante maior visibilidade e descoberta, enquanto o Codeberg oferece uma alternativa mais ética e alinhada a princípios de software livre.
Dessa forma, você aproveita o melhor dos dois mundos: mantém o repositório no GitHub para atrair novos usuários, mas oferece uma alternativa no Codeberg para quem prefere outra plataforma.

## Preparar o espelhamento de repositórios
Siga estas etapas para configurar o espelhamento do seu repositório GitHub para o Codeberg.
#### Passo 1: Crie uma conta no Codeberg
Se ainda não tiver uma conta, acesse [codeberg.org](https://codeberg.org) e registre-se.

#### Passo 2: Acesse o painel e inicie a migração
No painel do Codeberg, clique no ícone "+" localizado no topo da interface.
No menu suspenso, selecione Nova migração.

#### Passo 3: Escolha o tipo de migração
Na página de migração, escolha GitHub como origem do repositório, já que estamos transferindo de lá.
Nota: Isso criará um clone do repositório no Codeberg, sem excluir ou modificar o original no GitHub.
As instruções para o GitLab ou outros repositórios auto-hospedados são semelhantes e estão disponíveis na mesma interface.

#### Passo 4: Insira a URL do repositório
Digite a URL de clone do seu repositório no GitHub (ela deve terminar com .git).
Para localizar a URL: vá até o seu repositório no GitHub e copie a URL SSH ou HTTPS exibida no botão "Code".

#### Passo 5: (Opcional) Sincronize Pull Requests e dados adicionais
Se quiser que o Codeberg importe também os Pull Requests e outros dados, insira um Token de Acesso Pessoal do GitHub no campo apropriado.
Nota: O mesmo processo funciona para GitLab.

#### Passo 6: Credenciais para repositórios auto-hospedados
Se estiver migrando de uma instância Git auto-hospedada, insira as credenciais de login do seu servidor no campo correspondente.

#### Passo 7: Finalize a migração
Clique no botão **Migrar Repositório**. O Codeberg iniciará o processo de migração e, em breve, seu repositório estará disponível na plataforma.

#### Passo 8: (Opcional) Clone o repositório no Codeberg
Agora que o repositório foi migrado, você pode cloná-lo para sua máquina local a partir do Codeberg, se desejar trabalhar diretamente nele.

## Espelhamento de repositórios
Existem duas formas principais de configurar o espelhamento entre GitHub e Codeberg. 
### Opção 1: Usando a linha de comando do Git
#### Passo 1: Configurar remotos separados para GitHub e Codeberg
Adicionar o repositório remoto do GitHub. Normalmente este passo já está configurado automaticamente.

Substitua user pelo seu nome de usuário e repo pelo nome do repositório:
```
git remote add github https://github.com/user/repo.git
```
Adicionar o repositório remoto do Codeberg
Substitua user pelo seu nome de usuário e repo pelo nome do repositório:
```
git remote add codeberg https://codeberg.org/user/repo.git
```

#### Passo 2: Verificar se os remotos foram configurados corretamente
Após adicionar os remotos, execute o comando a seguir para listar os repositórios remotos configurados:
```
git remote -v
```

Se configurado corretamente, o resultado será algo como:

```
github    https://github.com/user/repo.git (fetch)  
github    https://github.com/user/repo.git (push)  
codeberg  https://codeberg.org/user/repo.git (fetch)  
codeberg  https://codeberg.org/user/repo.git (push)  
origin  https://github.com/sand-br/sandbr.git (fetch)
origin  https://github.com/sand-br/sandbr.git (push)
```
#### Passo 3: Configurar origin para enviar ao Codeberg também
Por padrão, o comando git push sem argumentos enviará alterações apenas ao repositório configurado como origin, que geralmente é o GitHub. Vamos configurá-lo para também incluir o Codeberg.

Adicione o repositório Codeberg como um URL adicional para origin:

```
git remote set-url --add --push origin https://codeberg.org/user/repo.git
```
Agora, ao usar git push, as alterações serão enviadas para ambos os serviços, GitHub e Codeberg.

Com isso, você configurou o espelhamento do seu repositório com sucesso! A partir de agora, todas as alterações feitas no repositório local serão enviadas para ambos os serviços ao executar git push.

***
### Opção 2: Configurando via arquivo `config`
Se preferir, você pode configurar o espelhamento editando diretamente o arquivo de configuração do Git. Esse método permite personalizar as configurações sem a necessidade de comandos na linha de comando.

#### O que é o arquivo config?
O arquivo config está localizado no diretório .git do seu repositório. Ele contém todas as configurações manipuladas por comandos como git remote ou git branch. Nesse caso, vamos editar o arquivo diretamente para configurar o espelhamento.

Passo a passo para editar o arquivo config
#### Passo 1: Acesse o diretório `.git`
Navegue até o diretório `.git` do seu repositório local e abra o arquivo `config` no editor de sua preferência

Você pode usar o Bloco de Nota ou o VSCode, por exemplo, ou outro editor de texto.

#### Passo 2: Configurar o arquivo
Certifique-se de substituir user pelo seu nome de usuário e repo pelo nome do repositório e então substitua as partes necessária do arquivo pelo seguinte:

```
[core]
    repositoryformatversion = 0
    filemode = true
    bare = false
    logallrefupdates = true
[remote "github"]
    url = https://github.com/user/repo.git
    fetch = +refs/heads/*:refs/remotes/github/*
[remote "codeberg"]
    url = https://codeberg.org/user/repo.git
    fetch = +refs/heads/*:refs/remotes/codeberg/*
[remote "origin"]
    url = https://github.com/user/repo.git
    fetch = +refs/heads/*:refs/remotes/origin/*
    pushurl = https://github.com/user/repo.git
    pushurl = https://codeberg.org/user/repo.git
[branch "main"]
    remote = origin
    merge = refs/heads/main
    pushRemote = origin
```
#### Passo 3: Salvar e sair
Após substituir os valores, salve o arquivo e feche o editor.

### Como isso funciona?
O GitHub (remote "github") e o Codeberg (remote "codeberg") têm suas URLs de fetch configuradas para sincronizar com seus repositórios.

O origin foi configurado com pushurl para enviar as alterações para ambos os serviços ao executar git push. Com isso, seu repositório está configurado para enviar e buscar alterações de GitHub e Codeberg automaticamente.

***
## Testando o espelhamento
Agora que você configurou o espelhamento, é hora de testar se tudo está funcionando corretamente.

#### Passo 1: Prepare um arquivo de teste
Que tal um:
```
Esta é uma mensagem de teste para o espelhamento de repositórios. 
Criado com a ajuda do incrível [Raavë Aires](https://github.com/raave-aires)
Se você está lendo isso tanto no GitHub quanto no Codeberg, parabéns! O espelhamento está funcionando perfeitamente. 🎉
```
#### Faça um git push
No terminal, ou na sua IDE, execute o seguinte comando a partir do diretório do seu repositório local:
```
git push
```
Passo 3: Verifique se as alterações foram sincronizadas
Acesse seu repositório no [Codeberg](https://codeberg.org/) e veja se as mudanças foram aplicadas.

Repita o mesmo processo no [GitHub](https://github.com/).

Se tudo foi configurado corretamente, as alterações estarão atualizadas em ambos os repositórios.

***
## Licença
Este conteúdo é licenciado sob a [Creative Commons Atribuição-NãoComercial-CompartilhaIgual 4.0 Internacional](https://creativecommons.org/licenses/by-nc-sa/4.0/legalcode.pt) (CC BY-NC-SA 4.0).

Você é livre para:
- Compartilhar — copiar e redistribuir o material em qualquer formato ou mídia.
- Adaptar — remixar, transformar e criar a partir do material.

Desde que respeite as condições:
- Atribuição — dê crédito apropriado, forneça um link para a licença e indique se alterações foram feitas.
- Uso Não Comercial — você não pode usar o material para fins comerciais.
- Compartilha Igual — se você remixar, transformar ou criar a partir do material, deverá distribuir suas contribuições sob a mesma licença.

### Como atribuir?
Use um destes modelos para fazer atribuição de autoria a mim:

Se você estiver apenas distribuindo sem modificar:
```
Este material foi criado por Raavë Aires e está disponível em <> e licenciado pela CC BY-NC-SA 4.0.
```

```
Baseado no trabalho de Raavë Aires, disponível em <>. Licenciado pela CC BY-NC-SA 4.0.
```

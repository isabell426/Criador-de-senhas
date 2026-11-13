
Criador de senhas, Um gerador de senhas que permite criar senhas aleatórias e
personalizadas, escolhendo o tamanho e os tipos de caracteres que serão
utilizados.
Funcionalidades
- Escolher o tamanho da senha por meio de um controle deslizante.
- Incluir ou não letras maiúsculas.
- Incluir ou não letras minúsculas.
- Incluir ou não números.
- Incluir ou não símbolos.
- Gerar senhas com aleatoriedade segura usando a API
  crypto.getRandomValues().
- Garantir pelo menos um caractere de cada categoria selecionada.
- Embaralhar os caracteres gerados.
- Copiar a senha para a área de transferência.
- Exibir uma classificação de força: fraca, média ou forte.
- Gerar uma senha automaticamente ao abrir a página.
Como funciona
1. O usuário escolhe o tamanho desejado para a senha.
2. Seleciona as categorias de caracteres que deseja incluir.
3. Clica em Gerar senha.
4. O sistema verifica se pelo menos uma categoria foi selecionada e se
   o tamanho é suficiente.
5. A senha é criada, embaralhada e exibida no campo correspondente.
6. O sistema avalia a força da senha.
7. O usuário pode clicar em Copiar para copiar a senha.
Tecnologias utilizadas
- HTML --- estrutura da página.
- CSS --- aparência e organização visual.
- JavaScript --- lógica de geração, avaliação e cópia da senha.
- Web Crypto API --- geração de valores aleatórios.
Como executar
1. Mantenha os arquivos index.html, style.css e script.js na
   estrutura do projeto (os nomes podem variar conforme sua
   implementação).
2. Abra o arquivo index.html em um navegador atualizado.
3. Escolha as opções e clique em Gerar senha.
A função de cópia utiliza navigator.clipboard. Esse recurso pode
exigir uma conexão segura (HTTPS ou localhost), dependendo do
navegador.

Validação e segurança
- O sistema avisa quando nenhuma categoria de caracteres foi
  selecionada.
- O sistema solicita o aumento do tamanho quando ele é menor que a
  quantidade de categorias selecionadas.
- A seleção de caracteres aleatórios utiliza
  crypto.getRandomValues(), em vez de Math.random().
- O embaralhamento evita manter os caracteres na ordem em que foram
  selecionados.
Observação sobre a força da senha
A classificação exibida (fraca, média ou forte) é uma estimativa simples
baseada no comprimento e na presença de diferentes categorias de
caracteres. Ela não representa uma auditoria completa da segurança da
senha.
Autor
Projeto acadêmico --- Criador de Senhas.


// ELEMENTOS HTML

const password = document.getElementById("password");
const length = document.getElementById("length");
const lengthValue = document.getElementById("lengthValue");

const uppercase = document.getElementById("uppercase");
const lowercase = document.getElementById("lowercase");
const numbers = document.getElementById("numbers");
const symbols = document.getElementById("symbols");

const generateBtn = document.getElementById("generateBtn");
const copyBtn = document.getElementById("copyBtn");

const message = document.getElementById("message");
const strengthText = document.getElementById("strengthText");


// ATUALIZAR TAMANHO

length.addEventListener("input", () => {
    lengthValue.textContent = length.value;
});


// ESCOLHER ÍNDICE ALEATÓRIO SEM VIÉS DE MÓDULO

function randomIndex(max) {
    const range = 0x100000000;
    const limit = Math.floor(range / max) * max;

    const values = new Uint32Array(1);

    do {
        crypto.getRandomValues(values);
    } while (values[0] >= limit);

    return values[0] % max;
}


// EMBARALHAR CARACTERES

function shuffle(array) {
    for (let i = array.length - 1; i > 0; i--) {
        const j = randomIndex(i + 1);

        [array[i], array[j]] = [array[j], array[i]];
    }

    return array;
}


// GERAR SENHA

function generatePassword() {

    const size = Number(length.value);

    const characterSets = [];

    if (uppercase.checked) {
        characterSets.push("ABCDEFGHIJKLMNOPQRSTUVWXYZ");
    }

    if (lowercase.checked) {
        characterSets.push("abcdefghijklmnopqrstuvwxyz");
    }

    if (numbers.checked) {
        characterSets.push("0123456789");
    }

    if (symbols.checked) {
        characterSets.push("!@#$%&*()-_=+?");
    }

    // VERIFICAR SE EXISTE ALGUMA OPÇÃO SELECIONADA

    if (characterSets.length === 0) {
        password.value = "";
        message.textContent = "Selecione pelo menos uma opção!";
        strengthText.textContent = "Indisponível";
        return;
    }

    // VERIFICAR SE O TAMANHO É SUFICIENTE

    if (size < characterSets.length) {
        password.value = "";
        message.textContent = "Aumente o tamanho da senha!";
        strengthText.textContent = "Indisponível";
        return;
    }

    // GARANTIR UM CARACTERE DE CADA CATEGORIA

    const selectedCharacters = [];

    characterSets.forEach(set => {
        selectedCharacters.push(
            set[randomIndex(set.length)]
        );
    });

    // JUNTAR TODOS OS CARACTERES DISPONÍVEIS

    const allCharacters = characterSets.join("");

    // COMPLETAR O TAMANHO DA SENHA

    while (selectedCharacters.length < size) {
        selectedCharacters.push(
            allCharacters[randomIndex(allCharacters.length)]
        );
    }

    // EMBARALHAR A SENHA

    const finalPassword = shuffle(selectedCharacters).join("");

    password.value = finalPassword;

    message.textContent = "Senha gerada com sucesso!";

    checkStrength(finalPassword);
}


// VERIFICAR FORÇA DA SENHA

function checkStrength(value) {

    let score = 0;

    if (value.length >= 12) score++;
    if (/[A-Z]/.test(value)) score++;
    if (/[a-z]/.test(value)) score++;
    if (/[0-9]/.test(value)) score++;
    if (/[^A-Za-z0-9]/.test(value)) score++;

    if (score <= 2) {
        strengthText.textContent = "FRACA";
        strengthText.style.color = "#ff5555";
    } else if (score <= 3) {
        strengthText.textContent = "MÉDIA";
        strengthText.style.color = "#ffcc55";
    } else {
        strengthText.textContent = "FORTE";
        strengthText.style.color = "#39ff88";
    }
}


// COPIAR SENHA

copyBtn.addEventListener("click", async () => {

    if (!password.value) {
        message.textContent = "Gere uma senha primeiro!";
        return;
    }

    try {
        await navigator.clipboard.writeText(password.value);

        message.textContent = "Senha copiada com sucesso!";

    } catch (error) {
        message.textContent = "Não foi possível copiar. Verifique as permissões.";
    }

});


// EVENTO DO BOTÃO GERAR

generateBtn.addEventListener("click", generatePassword);


// GERAR UMA SENHA AO ABRIR A PÁGINA

generatePassword();

 


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

 

# Ferramenta de Unfollow para Instagram

Um script para automatizar o processo de "deixar de seguir".

> **AVISO IMPORTANTE: LEIA ANTES DE USAR**
> 
> O uso de automação viola os Termos de Serviço do Instagram. Sua conta pode ser **bloqueada ou banida**. Use esta ferramenta com moderação e por sua conta e risco. Recomendamos não deixar de seguir mais de 150-200 pessoas por dia para reduzir os riscos.

---

## Como Usar o Script (Passo a Passo)

1.  **Acesse seu Perfil:** Abra o Instagram no seu navegador (Chrome, Firefox, etc.) e vá para a sua página de perfil.
2.  **Abra a Lista "Seguindo":** Clique no número de pessoas que você está `"seguindo"` para abrir a lista completa.
3.  **Role a Página:** Role a lista um pouco para baixo para carregar as contas que você deseja deixar de seguir. O script só funcionará nas contas que estão visíveis na tela.
4.  **Abra o Console do Desenvolvedor:**
    * No Windows/Linux: Pressione `Ctrl` + `Shift` + `J`
    * No Mac: Pressione `Cmd` + `Option` + `J`
    
    Uma nova janela ou painel aparecerá no seu navegador. Certifique-se de estar na aba `"Console"`.
5.  **Copie e Cole o Código:** Copie o código JavaScript abaixo e cole-o no Console que você acabou de abrir.
6.  **Execute o Script:** Pressione `Enter`. A automação começará e você verá mensagens no console para cada pessoa que deixar de seguir.
7.  **Para Parar:** Para parar o script a qualquer momento, basta fechar a aba do Instagram ou atualizar a página.

---

## Código JavaScript

Copie o código abaixo, cole no console do seu navegador e pressione Enter para executar.

```javascript
// --- Configurações ---
// Defina o número máximo de pessoas para dar unfollow nesta sessão.
const maxUnfollows = 50; 

// Intervalo de tempo em segundos (mínimo e máximo) entre cada unfollow.
// Isso simula o comportamento humano para evitar bloqueios.
const minDelaySeconds = 8;
const maxDelaySeconds = 20;
// --------------------

// Função para criar uma pausa com tempo aleatório
function sleep(seconds) {
    console.log(`%cPróxima ação em ${seconds.toFixed(1)} segundos...`, 'color: #84cc16');
    return new Promise(resolve => setTimeout(resolve, seconds * 1000));
}

// Função principal da automação
async function startUnfollow() {
    console.log('%c--- INICIANDO SCRIPT DE UNFOLLOW ---', 'color: #22d3ee; font-size: 16px; font-weight: bold;');
    console.log(`%cAVISO: O script tentará deixar de seguir até ${maxUnfollows} perfis.`, 'color: #f87171;');

    // Seleciona todos os botões "Seguindo" visíveis na tela.
    // O seletor do Instagram pode mudar. Se não funcionar, ele precisará ser atualizado.
    const followingButtons = Array.from(document.querySelectorAll('button')).filter(button => button.textContent === 'Seguindo');

    if (followingButtons.length === 0) {
        console.error('%cERRO: Nenhum botão "Seguindo" foi encontrado. Role a lista de "seguindo" para carregar os perfis e tente novamente.', 'color: #facc15;');
        return;
    }

    let unfollowedCount = 0;
    for (const button of followingButtons) {
        if (unfollowedCount >= maxUnfollows) {
            console.log(`%cLimite de ${maxUnfollows} unfollows atingido. Encerrando.`, 'color: #22c55e;');
            break;
        }

        // Rola a página para garantir que o botão esteja visível
        button.scrollIntoView({ behavior: 'smooth', block: 'center' });
        await sleep(1); // pequena pausa para a rolagem

        // 1. Clica no botão "Seguindo"
        button.click();
        await sleep(1.5); // Espera o pop-up de confirmação aparecer

        // 2. Encontra e clica no botão de confirmação "Deixar de seguir"
        const confirmButton = Array.from(document.querySelectorAll('button')).find(b => b.textContent === 'Deixar de seguir');
        
        if (confirmButton) {
            const userHandle = button.closest('div[role="dialog"] > div > div > div:nth-child(2) > div:nth-child(1) > div > div > span > a')?.textContent || 'usuário desconhecido';
            confirmButton.click();
            unfollowedCount++;
            console.log(`%c(${unfollowedCount}/${maxUnfollows}) Deixou de seguir: @${userHandle}`, 'color: #e879f9;');
        } else {
            // Se não achar o botão de confirmação, pode ser um perfil que não abriu o pop-up.
            // Clicar novamente no botão "Seguindo" geralmente fecha a ação.
            console.warn('%cAVISO: Não foi possível encontrar o botão de confirmação. Tentando fechar e pular para o próximo.', 'color: #facc15;');
            if(button.textContent === 'Seguir'){ // Se o botão mudou para 'Seguir', o unfollow funcionou
                 unfollowedCount++;
                 console.log(`%c(${unfollowedCount}/${maxUnfollows}) Deixou de seguir um perfil (método alternativo).`, 'color: #e879f9;');
            } else {
                button.click(); // Tenta fechar
            }
        }
        
        // Pausa longa e aleatória antes de ir para o próximo
        const randomDelay = Math.random() * (maxDelaySeconds - minDelaySeconds) + minDelaySeconds;
        await sleep(randomDelay);
    }

    console.log(`%c--- SCRIPT CONCLUÍDO ---`, 'color: #22d3ee; font-size: 16px; font-weight: bold;');
    console.log(`%cTotal de perfis que deixaram de ser seguidos nesta sessão: ${unfollowedCount}`, 'color: #22c55e;');
}

// Inicia a função
startUnfollow();

# Aplicação de Chats e Configurações API

O objetivo deste desafio é explorar o desenvolvimento de aplicações práticas utilizando o Azure OpenAI, incluindo chamadas de API e integração com o Semantic Kernel.

**Azure OpenAI | Inteligência Artificial (IA)**

**Full-Stack | Básico**

## API Azure OpenAI

O gerenciamento e a interação com modelos e recursos do Azure OpenAI são divididos em três superfícies de API principais:

* Plano de controle
* Plano de dados - autoria
* Plano de dados - inferência

Cada superfície/especificação de API encapsula um conjunto diferente de recursos do Azure OpenAI. Cada API tem seu próprio conjunto exclusivo de versões de API de pré-visualização e estáveis/geralmente disponíveis (GA). As versões de pré-visualização atualmente tendem a seguir uma cadência mensal.

## Autenticação

O Azure OpenAI fornece dois métodos para autenticação. Você pode usar Chaves de API ou Microsoft Entra ID.

* **Autenticação de chave de API**: para esse tipo de autenticação, todas as solicitações de API devem incluir a chave de API no `api-key` cabeçalho HTTP. O [Quickstart](https://learn.microsoft.com/en-us/azure/ai-services/openai/chatgpt-quickstart?tabs=command-line%2Ckeyless%2Ctypescript-keyless%2Cpython-new&pivots=programming-language-studio) fornece orientação sobre como fazer chamadas com esse tipo de autenticação.

* **Autenticação do Microsoft Entra ID**: Você pode autenticar uma chamada de API usando um token do Microsoft Entra. Os tokens de autenticação são incluídos em uma solicitação como o `Authorization` cabeçalho. O token fornecido deve ser precedido por `Bearer`, por exemplo `Bearer YOUR_AUTH_TOKEN`. Você pode ler nosso guia prático sobre [autenticação com o Microsoft Entra ID](https://learn.microsoft.com/en-us/azure/ai-services/openai/how-to/managed-identity).

## Controle de versão da API REST

As APIs de serviço são versionadas usando o api-versionparâmetro query. Todas as versões seguem a estrutura de data YYYY-MM-DD. Por exemplo:

```http
POST https://YOUR_RESOURCE_NAME.openai.azure.com/openai/deployments/YOUR_DEPLOYMENT_NAME/chat/completions?api-version=2024-06-01
```

## Inferência do plano de dados

O restante do artigo aborda a versão mais recente do GA da especificação de inferência do plano de dados do Azure OpenAI `2024-10-21`.

Se você estiver procurando por documentação sobre a versão mais recente da API de visualização, consulte a [versão mais recente da API de inferência do plano de dados de visualização](https://learn.microsoft.com/en-us/azure/ai-services/openai/reference-preview).

## Introdução ao Kernel Semântico

![alt text](https://learn.microsoft.com/en-us/semantic-kernel/media/enterprise-ready.png)

![alt text](https://learn.microsoft.com/en-us/semantic-kernel/media/designed-for-modular-extensibility.png)

Em apenas alguns passos, você pode construir seu primeiro agente de IA com Semantic Kernel em Python, .NET ou Java. Este guia mostrará como...

* Instale os pacotes necessários
* Crie uma conversa de ida e volta com uma IA
* Dê a um agente de IA a capacidade de executar seu código
* Observe a IA criar planos rapidamente

## Instalando o SDK

As instruções para acessar o `SemanticKernel`pacote Python estão disponíveis [**aqui**](https://pypi.org/project/semantic-kernel/).

```pypi
pip install semantic-kernel
```

Para começar, siga estes passos:

1. Clonar o [repositório do Kernel Semântico](https://github.com/microsoft/semantic-kernel)
2. Abra o repositório no Visual Studio Code
3. Navegue até [_/python/samples/getting_started](https://github.com/microsoft/semantic-kernel/tree/main/python/samples/getting_started)
4. Abra 00-getting-started.ipynb para começar a configurar seu ambiente e criar seu primeiro agente de IA!

## Escrevendo seu primeiro aplicativo de console

```python
import asyncio

from semantic_kernel import Kernel
from semantic_kernel.utils.logging import setup_logging
from semantic_kernel.functions import kernel_function
from semantic_kernel.connectors.ai.open_ai import AzureChatCompletion
from semantic_kernel.connectors.ai.function_choice_behavior import FunctionChoiceBehavior
from semantic_kernel.connectors.ai.chat_completion_client_base import ChatCompletionClientBase
from semantic_kernel.contents.chat_history import ChatHistory
from semantic_kernel.functions.kernel_arguments import KernelArguments

from semantic_kernel.connectors.ai.open_ai.prompt_execution_settings.azure_chat_prompt_execution_settings import (
    AzureChatPromptExecutionSettings,
)

async def main():
    # Initialize the kernel
    kernel = Kernel()

    # Add Azure OpenAI chat completion
    chat_completion = AzureChatCompletion(
        deployment_name="your_models_deployment_name",
        api_key="your_api_key",
        base_url="your_base_url",
    )
    kernel.add_service(chat_completion)

    # Set the logging level for  semantic_kernel.kernel to DEBUG.
    setup_logging()
    logging.getLogger("kernel").setLevel(logging.DEBUG)

    # Add a plugin (the LightsPlugin class is defined below)
    kernel.add_plugin(
        LightsPlugin(),
        plugin_name="Lights",
    )

    # Enable planning
    execution_settings = AzureChatPromptExecutionSettings()
    execution_settings.function_choice_behavior = FunctionChoiceBehavior.Auto()

    # Create a history of the conversation
    history = ChatHistory()

    # Initiate a back-and-forth chat
    userInput = None
    while True:
        # Collect user input
        userInput = input("User > ")

        # Terminate the loop if the user says "exit"
        if userInput == "exit":
            break

        # Add user input to the history
        history.add_user_message(userInput)

        # Get the response from the AI
        result = await chat_completion.get_chat_message_content(
            chat_history=history,
            settings=execution_settings,
            kernel=kernel,
        )

        # Print the results
        print("Assistant > " + str(result))

        # Add the message from the agent to the chat history
        history.add_message(result)

# Run the main function
if __name__ == "__main__":
    asyncio.run(main())
```

## Conclusão

Neste projeto aprendemos a como começar rapidamente com o Semantic Kernel construindo um agente de IA simples que pode interagir com um serviço de IA e executar seu código. Para ver mais exemplos e aprender como construir agentes de IA mais complexos, [confira nossos exemplos detalhados](https://learn.microsoft.com/en-us/semantic-kernel/get-started/detailed-samples?pivots=programming-language-python).

## Links Úteis

* [**OpenAI Reference**](https://learn.microsoft.com/en-us/azure/ai-services/openai/reference)

* [**Semantic Kernel**](https://learn.microsoft.com/en-us/semantic-kernel/overview/)

##

Projeto desenvolvido durante o [**Bootcamp Microsoft AI for Tech - OpenAI Services**](https://www.dio.me/bootcamp/microsoft-azure-open-ai), fornecido pela [**DIO**](https://www.dio.me/)

##

- [By Páucinha](https://github.com/Paucinha)
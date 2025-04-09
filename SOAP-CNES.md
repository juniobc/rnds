## Integração com o CNES - Consulta de Estabelecimento de Saúde

**Objetivo:** Demonstrar a capacidade da equipe em realizar integrações com a Rede Nacional de Dados em Saúde (RNDS), especificamente com o Cadastro Nacional de Estabelecimentos de Saúde (CNES).

**Descrição:**

Esta integração permite que sistemas terceiros consultem informações detalhadas de um estabelecimento de saúde específico, utilizando o código CNES como identificador. A comunicação é realizada através de serviços web SOAP, um padrão para troca de informações estruturadas.

**Fluxo da Integração:**

1.  **Requisição:** O sistema terceiro envia uma requisição SOAP para o serviço do CNES, contendo o código CNES do estabelecimento a ser consultado.
2.  **Autenticação:** A requisição inclui um token de segurança (`UsernameToken`) no cabeçalho SOAP para autenticação no serviço do CNES.
3.  **Processamento:** O serviço do CNES processa a requisição, busca as informações do estabelecimento de saúde no banco de dados e prepara a resposta.
4.  **Resposta:** O serviço do CNES retorna uma mensagem SOAP contendo os dados do estabelecimento de saúde.
5.  **Retorno:** Nossa aplicação recebe a resposta SOAP, extrai os dados relevantes e os retorna para o sistema terceiro em um formato estruturado (dicionário Python).

**Detalhes Técnicos:**

* **Padrão de Mensagens:** SOAP
* **Autenticação:** `UsernameToken`
* **Serviço Consultado:** CNES
* **Identificador:** Código CNES

**Exemplo de Requisição (XML):**

```xml
<soap:Envelope xmlns:soap="[http://www.w3.org/2003/05/soap-envelope](http://www.w3.org/2003/05/soap-envelope)" xmlns:cnes="[http://servicos.saude.gov.br/cnes/v1r0/cnesservice](http://servicos.saude.gov.br/cnes/v1r0/cnesservice)" xmlns:cod="[http://servicos.saude.gov.br/schema/cnes/v1r0/codigocnes](http://servicos.saude.gov.br/schema/cnes/v1r0/codigocnes)">
    <soap:Header>
        <wsse:Security xmlns:wsse="[http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd](http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd)" xmlns:wsu="[http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd](http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd)">
            <wsse:UsernameToken wsu:Id="UsernameToken-F31A3FB33EEECDF5B617325776425894">
                <wsse:Username>CNES.PUBLICO</wsse:Username>
                <wsse:Password Type="[http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-username-token-profile-1.0#PasswordText](http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-username-token-profile-1.0#PasswordText)">cnes#2015public</wsse:Password>
            </wsse:UsernameToken>
        </wsse:Security>
    </soap:Header>
    <soap:Body>
        <cnes:requestConsultarEstabelecimentoSaude>
            <cod:CodigoCNES>
                <cod:codigo>7157681</cod:codigo>
            </cod:CodigoCNES>
        </cnes:requestConsultarEstabelecimentoSaude>
    </soap:Body>
</soap:Envelope>

Exemplo de Retorno (nosso código):

Returns:
    dict:
        - Se sucesso: return {
            "codigo_procedimento": codigo,
            "nome_procedimento": nome,
            "forma_organizacao": {
                "codigo": forma_organizacao_codigo,
                "nome": forma_organizacao_nome,
                "subgrupo": {
                    "codigo": subgrupo_codigo,
                    "nome": subgrupo_nome,
                    "grupo": {
                        "codigo": grupo_codigo,
                        "nome": grupo_nome
                    }
                }
            },
            "competencia_inicial": competencia_inicial,
            "finalidade_publicacao": finalidade_publicacao,
            "documento_publicacao": {
                "numero_documento": numero_documento,
                "tipo_documento": {
                    "codigo": tipo_documento_codigo,
                    "nome": tipo_documento_nome
                },
                "orgao_origem": {
                    "codigo": orgao_origem_codigo,
                    "nome": orgao_origem_nome
                },
                "data_publicacao": data_publicacao
            },
            "modalidades_atendimento": modalidades_atendimento,
            "complexidade": complexidade,
            "tipo_financiamento": {
                "codigo": tipo_financiamento_codigo,
                "nome": tipo_financiamento_nome
            },
            "instrumentos_registro": instrumentos_registro,
            "quantidade_maxima": quantidade_maxima,
            "sexo_permitido": sexo_permitido,
            "idade_minima_permitida": {
                "quantidade": idade_minima_quantidade,
                "unidade": idade_minima_unidade
            },
            "idade_maxima_permitida": {
                "quantidade": idade_maxima_quantidade,
                "unidade": idade_maxima_unidade
            },
            "valor_sh": valor_sh,
            "valor_sa": valor_sa,
            "valor_sp": valor_sp,
            "atributos_complementares": atributos_complementares,
            "descricao": descricao,
            "cbos_vinculados": cbos_vinculados,
            "categorias_cbo_vinculadas": categorias_cbo_vinculadas,
            "servicos_classificacoes_vinculados": servicos_classificacoes_vinculados,
            "origens_sia_sih": origens_sia_sih,
            "renases_vinculadas": renases_vinculadas,
            "cids_vinculados": cids_vinculados,
            "habilitacoes_vinculadas": habilitacoes_vinculadas,
            "regras_condicionadas_vinculadas": regras_condicionadas_vinculadas
        }
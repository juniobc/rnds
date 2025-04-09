## Integração com o Web Service do Cartão Nacional de Saúde (CNS) - HL7 FHIR

**Objetivo:** Integrar sistemas de terceiros ao Web Service do Cartão Nacional de Saúde (CNS) utilizando o padrão HL7 FHIR em formato XML.

**Descrição:**
Esta integração permite que sistemas externos consultem informações de pacientes utilizando o padrão HL7 FHIR.  A comunicação é realizada via serviços web SOAP.

**Fluxo da Integração:**
1.  **Requisição:** Sistemas terceiros enviam uma requisição SOAP para o serviço do CNS, formatada em HL7 FHIR (XML).
2.  **Autenticação:** A requisição SOAP inclui um token de segurança (`UsernameToken`) no cabeçalho.
3.  **Processamento:** O serviço do CNS processa a requisição e busca as informações do paciente.
4.  **Resposta:** O serviço do CNS retorna uma mensagem SOAP contendo os dados do paciente em HL7 FHIR (XML).
5.  **Retorno:** Nossa aplicação recebe a resposta SOAP, extrai os dados relevantes e os retorna para o sistema terceiro em um formato estruturado (dicionário Python), mapeando os campos do FHIR para um formato mais amigável.

**Detalhes Técnicos:**
* **Padrão de Mensagens:** SOAP
* **Padrão de Dados:** HL7 FHIR (XML)
* **Autenticação:** `UsernameToken`
* **Serviço Consultado:** CNS
* **Operação:** Consulta de Paciente

**Exemplo de Requisição (XML):**

```xml
<soap:Envelope xmlns:soap="[http://www.w3.org/2003/05/soap-envelope](http://www.w3.org/2003/05/soap-envelope)" xmlns:urn="urn:ihe:iti:xds-b:2007" xmlns:urn1="urn:oasis:names:tc:ebxml-regrep:xsd:lcm:3.0" xmlns:urn2="urn:oasis:names:tc:ebxml-regrep:xsd:rim:3.0" xmlns:urn3="urn:ihe:iti:xds-b:2007">
    <soap:Body>
        <PRPA_IN201305UV02 xsi:schemaLocation="urn:hl7-org:v3 ./schema/HL7V3/NE2008/multicacheschemas/PRPA_IN201305UV02.xsd" ITSVersion="XML_1.0" xmlns:xsi="[http://www.w3.org/2001/XMLSchema-instance](http://www.w3.org/2001/XMLSchema-instance)" xmlns="urn:hl7-org:v3">
            <id root="2.16.840.1.113883.4.714" extension="123456"/>
            <creationTime value="20070428150301"/>
            <interactionId root="2.16.840.1.113883.1.6" extension="PRPA_IN201305UV02"/>
            <processingCode code="T"/>
            <processingModeCode code="T"/>
            <acceptAckCode code="AL"/>
            <receiver typeCode="RCV">
                <device classCode="DEV" determinerCode="INSTANCE">
                <id root="2.16.840.1.113883.3.72.6.5.100.85"/>
                </device>
            </receiver>
            <sender typeCode="SND">
                <device classCode="DEV" determinerCode="INSTANCE">
                <id root="2.16.840.1.113883.3.72.6.2"/>
                <name>[SYSTEMCODE]</name>
                </device>
            </sender>
            <controlActProcess classCode="CACT" moodCode="EVN">
                <code code="PRPA_TE201305UV02" codeSystem="2.16.840.1.113883.1.6"/>
                <queryByParameter>
                <queryId root="1.2.840.114350.1.13.28.1.18.5.999" extension="1840997084"/>
                <statusCode code="new"/>
                <responseModalityCode code="R"/>
                <responsePriorityCode code="I"/>
                <parameterList>
                    <livingSubjectId>
                    <value root="2.16.840.1.113883.13.237" extension="{cpf}"/>
                    <semanticsText>LivingSubject.id</semanticsText>
                    </livingSubjectId>
                </parameterList>
                </queryByParameter>
            </controlActProcess>
        </PRPA_IN201305UV02>
    </soap:Body>
</soap:Envelope>

```


**Exemplo de Tratamento da Resposta**

```python
id_mapping = [
        ("2.16.840.1.113883.13.236", "asOtherIDs", "cnss"),
        ("2.16.840.1.113883.13.237", "asOtherIDs", "cpf"),
        ("2.16.840.1.113883.13.243", "asOtherIDs", "rg"),
        ("2.16.840.1.113883.13.245", "asOtherIDs", "rg_emissor"),
        ("2.16.840.1.113883.4.707", "asOtherIDs", "rg_uf_emissao"),
        ("2.16.840.1.113883.13.243.1", "asOtherIDs", "data_emissao_rg"),
        ("2.16.840.1.113883.13.238", "asOtherIDs", "cnh"),
        ("2.16.840.1.113883.13.239", "asOtherIDs", "titulo_eleitor"),
        ("2.16.840.1.113883.13.244", "asOtherIDs", "nis"),
        ("2.16.840.1.113883.13.240", "asOtherIDs", "passaporte"),
        ("", "name", "nome_completo"),
        ("", "personalRelationship", "nome_social"),
        ("", "birthTime", "data_nascimento"),
        ("", "personalRelationship", "nome_mae"),
        ("", "personalRelationship", "nome_pai"),
        ("", "personalRelationship", "vivo"),
        ("", "personalRelationship", "sexo"),
        ("", "raceCode", "raca_cor"),
        ("", "ethnicGroupCode", "etnia"),
        ("", "birthPlace", "Municipio de Nascimento"),
        ("", "addr", "endereco"),
        ("", "telecom", "telefone"),
        ("", "telecom", "email"),
    ]
```

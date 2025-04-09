## Integração do Registro de Informações de Regulação Assistencial (RIRA) com a RNDS - HL7 FHIR (JSON)

**Objetivo:** Integrar sistemas próprios de Registro de Informações de Regulação Assistencial (RIRA) à Rede Nacional de Dados em Saúde (RNDS) utilizando o padrão HL7 FHIR no formato JSON.

**Descrição:**
Esta integração permite o envio de informações de regulação assistencial de sistemas de terceiros para a RNDS, utilizando o padrão HL7 FHIR no formato JSON.

**Fluxo da Integração:**

1.  **Requisição:** Sistemas terceiros enviam dados de regulação assistencial para a RNDS em formato JSON, seguindo o padrão HL7 FHIR.

2.  **Autenticação:** A requisição é autenticada (detalhes específicos de autenticação podem variar, mas geralmente envolvem tokens ou certificados).

3.  **Processamento:** A RNDS recebe e processa os dados, validando-os conforme o perfil de Regulação Assistencial definido.

4.  **Armazenamento:** Os dados são armazenados na RNDS.

5.  **Resposta:** A RNDS retorna um código de sucesso ou erro, indicando o resultado da operação.

**Detalhes Técnicos:**

* **Padrão de Mensagens:** HL7 FHIR
* **Formato:** JSON
* **Serviço Consultado:** RNDS
* **Operação:** Envio de dados de Regulação Assistencial


**Exemplo de Tratamento da Resposta (Python com FHIR.resource):**

```python
from pydantic import BaseModel, Field
from typing import Optional, List, Dict

class ConsultaRequest(BaseModel):
    cns: Optional[str] = None
    cpf: Optional[str] = None
    nome: Optional[str] = None
    nome_mae: Optional[str] = None

class Endereco(BaseModel):
    cidade: Optional[str] = None
    estado: Optional[str] = None
    cep: Optional[str] = None
    pais: Optional[str] = None
    numero: Optional[str] = None
    nome_rua: Optional[str] = None
    setor: Optional[str] = None

class CNSInfo(BaseModel):
    cns: List[str]
    statusCns: List[str]

class ConsultaResponse(BaseModel):
    cnss: Optional[CNSInfo] = None
    cpf: Optional[str] = None
    rg: Optional[str] = None
    rg_emissor: Optional[str] = None
    rg_uf_emissao: Optional[str] = None
    data_emissao_rg: Optional[str] = None
    cnh: Optional[str] = None
    titulo_eleitor: Optional[str] = None
    nis: Optional[str] = None
    passaporte: Optional[str] = None
    nome_completo: Optional[str] = None
    nome_social: Optional[str] = None
    data_nascimento: Optional[str] = None
    nome_mae: Optional[str] = None
    nome_pai: Optional[str] = None
    vivo: Optional[str] = None
    sexo: Optional[str] = None
    raca_cor: Optional[str] = None
    etnia: Optional[str] = None
    municipio_nascimento: Optional[str] = None
    endereco: Optional[Endereco] = None
    telefone: Optional[str] = None
    email: Optional[str] = None

class CadastroNaoEncontradoResponse(BaseModel):
    tipo_registro: Optional[str] = None
    data_registro: Optional[str] = None
    cpf: Optional[str] = None
    cns: Optional[str] = None
    unidade_registro: Optional[str] = None
    equipe_registro: Optional[str] = None
    profissional_registro: Optional[str] = None

# Novo modelo para incluir os metadados
class CadastroNaoEncontradoResponseWrapper(BaseModel):
    registros: List[CadastroNaoEncontradoResponse]
    total_registros: int
    paginas_restantes: int

class CID10APS(BaseModel):
    id: Optional[int] = None
    codigo: str
    diagnostico: str
```

**Exemplo de Requisição (JSON):**

```json
{
  "resourceType": "Bundle",
  "identifier": {
    "system": "[http://www.saude.gov.br/fhir/r4/NamingSystem/BRRNDS-31239](http://www.saude.gov.br/fhir/r4/NamingSystem/BRRNDS-31239)",
    "value": "{{$guid}}"
  },
  "type": "document",
  "timestamp": "{{random_date}}",
  "meta": {
    "lastUpdated": "{{random_date}}"
  },
  "entry": [
    {
      "fullUrl": "urn:uuid:transient-0",
      "resource": {
        "resourceType": "Composition",
        "meta": {
          "lastUpdated": "{{random_date}}",
          "profile": [
            "[http://www.saude.gov.br/fhir/r4/StructureDefinition/BRRegulacaoAssistencial](http://www.saude.gov.br/fhir/r4/StructureDefinition/BRRegulacaoAssistencial)"
          ]
        },
        "status": "final",
        "type": {
          "coding": [
            {
              "system": "[http://www.saude.gov.br/fhir/r4/CodeSystem/BRTipoDocumento](http://www.saude.gov.br/fhir/r4/CodeSystem/BRTipoDocumento)",
              "code": "RA"
            }
          ]
        },
        "category": [
          {
            "coding": [
              {
                "system": "[http://www.saude.gov.br/fhir/r4/CodeSystem/BRModalidadeAssistencial](http://www.saude.gov.br/fhir/r4/CodeSystem/BRModalidadeAssistencial)",
                "code": "09"
              }
            ]
          }
        ],
        "subject": {
          "identifier": {
            "system": "[http://www.saude.gov.br/fhir/r4/StructureDefinition/BRIndividuo-1.0](http://www.saude.gov.br/fhir/r4/StructureDefinition/BRIndividuo-1.0)",
            "value": "{{meu_cns}}"
          }
        },
        "date": "2022-05-09",
        "author": [
          {
            "identifier": {
              "system": "[http://www.saude.gov.br/fhir/r4/StructureDefinition/BREstabelecimentoSaude-1.0](http://www.saude.gov.br/fhir/r4/StructureDefinition/BREstabelecimentoSaude-1.0)",
              "value": "0010464"
            }
          }
        ],
        "title": "Regulação Assistencial",
        "event":[
                        {
                        "code":[{
                            "coding": [
                                                {
                                                "system": "[http://www.saude.gov.br/fhir/r4/CodeSystem/BRStatusRegulacaoAssistencial](http://www.saude.gov.br/fhir/r4/CodeSystem/BRStatusRegulacaoAssistencial)",
                                                "code": "pending"
                                                }
                                            ]
                                        }],
                        "period": {
                                        "start": "2023-04-09T00:00:00.000000Z",
                                        "end": "2023-04-09T00:00:00.000000Z"
                                        },
                        "detail":[
                                                {
                                                
                                                "reference": "urn:uuid:transient-2"
                                                },
                                                {
                                                
                                                "identifier": {
                                                                "system": "[http://www.saude.gov.br/fhir/r4/StructureDefinition/BREstabelecimentoSaude-1.0](http://www.saude.gov.br/fhir/r4/StructureDefinition/BREstabelecimentoSaude-1.0)",
                                                                "value": "0010464"
                                                                }
                                                }
                                        ]
                        },
                        {
                        "code":[{
                            "coding": [
                                                {
                                                "system": "[http://www.saude.gov.br/fhir/r4/CodeSystem/BRStatusRegulacaoAssistencial](http://www.saude.gov.br/fhir/r4/CodeSystem/BRStatusRegulacaoAssistencial)",
                                                "code": "returned-to-requester"
                                                }
                                            ]
                                        }],
                        "period": {
                                        "start": "2023-04-09T00:00:00.000000Z",
                                        "end": "2023-04-09T00:00:00.000000Z"
                                        },
                        "detail":[
                                                {
                                                
                                                "identifier": {
                                                                "system": "[http://www.saude.gov.br/fhir/r4/StructureDefinition/BREstabelecimentoSaude-1.0](http://www.saude.gov.br/fhir/r4/StructureDefinition/BREstabelecimentoSaude-1.0)",
                                                                "value": "0010464"
                                                                }
                                                }
                                        ]
                        },
                        {
                        "code":[{
                            "coding": [
                                                {
                                                "system": "[http://www.saude.gov.br/fhir/r4/CodeSystem/BRStatusRegulacaoAssistencial](http://www.saude.gov.br/fhir/r4/CodeSystem/BRStatusRegulacaoAssistencial)",
                                                "code": "booked"
                                                }
                                            ]
                                        }],
                        "period": {
                                        "start": "2023-04-09T00:00:00.000000Z",
                                        "end": "2023-04-09T00:00:00.000000Z"
                                        },
                        "detail":[
                                                {
                                                
                                                "identifier": {
                                                                "system": "[http://www.saude.gov.br/fhir/r4/StructureDefinition/BREstabelecimentoSaude-1.0](http://www.saude.gov.br/fhir/r4/StructureDefinition/BREstabelecimentoSaude-1.0)",
                                                                "value": "0010464"
                                                                }
                                                },
                                                {
                                                
                                                "reference": "urn:uuid:transient-1"
                                                }
                                        ]
                        }
                                    ],
        "section": [
          {
            "entry": [
              {
                "reference": "urn:uuid:transient-1"
              }
            ]
          }
        ]
      }
    },
    {
      "fullUrl": "urn:uuid:transient-1",
      "resource": {
        "resourceType": "Appointment",
        "meta": {
                        "lastUpdated": "2022-05-09T17:31:16.551205Z",
                        "profile": [
                                            "[http://www.saude.gov.br/fhir/r4/StructureDefinition/BRAgendamentoRegulacaoAssistencial](http://www.saude.gov.br/fhir/r4/StructureDefinition/BRAgendamentoRegulacaoAssistencial)"
                                           ]
                        },
        "status": "booked",
        "start": "2023-04-09T00:00:00.000000Z",
        "end": "2023-04-09T00:00:00.000000Z",
        "serviceCategory": [{
                        "coding": [{
                                            "system": "[http://www.saude.gov.br/fhir/r4/CodeSystem/BRModalidadeAssistencial](http://www.saude.gov.br/fhir/r4/CodeSystem/BRModalidadeAssistencial)",
                                            "code": "09"
                                            }]
                        }],
        "serviceType": [{
                        "coding": [{
                                            "system": "[http://www.saude.gov.br/fhir/r4/CodeSystem/BRTabelaSUS](http://www.saude.gov.br/fhir/r4/CodeSystem/BRTabelaSUS)",
                                            "code": "0209010037"
                                            }]
                        }],
        "specialty": [{
                        "coding": [{
                                            "system": "[http://www.saude.gov.br/fhir/r4/CodeSystem/BRCBO](http://www.saude.gov.br/fhir/r4/CodeSystem/BRCBO)",
                                            "code": "225265"
                                            }]
                        }],
        "appointmentType": {
                        "coding": [
                                            {
                                            "system": "[http://hl7.org/fhir/request-priority](http://hl7.org/fhir/request-priority)",
                                            "code": "urgent"
                                            }
                                        ]
                        },
        "reasonReference": [
                                        {
                                        "reference": "urn:uuid:transient-3" 
                                        }
                                    ],
        "created": "2023-03-09",
        "basedOn": [
                                        {
                                        "reference": "urn:uuid:transient-2" 
                                        }
                                    ],
        "participant": [
                                        {
                                        "type": [{
                                                        "coding": [
                                                                            {
                                                                            "system": "[http://www.saude.gov.br/fhir/r4/CodeSystem/BRTipoParticipante](http://www.saude.gov.br/fhir/r4/CodeSystem/BRTipoParticipante)",
                                                                            "code": "PCT"
                                                                            }
                                                                        ]
                                                        }],
                                        "actor":{
                                                        "identifier": {
                                                                        "system": "[http://www.saude.gov.br/fhir/r4/StructureDefinition/BRIndividuo-1.0](http://www.saude.gov.br/fhir/r4/StructureDefinition/BRIndividuo-1.0)",
                                                                        "value": "{{meu_cns}}"
                                                                        }
                                                        },
                                        "status":"accepted"
                                        }
                                    ]
      }
    },
    {
      "fullUrl": "urn:uuid:transient-3",
      "resource": {
        "resourceType": "Condition",
        "meta": {
                        "lastUpdated": "2022-05-09T17:37:55.000322Z",
                        "profile": [
                                            "[http://www.saude.gov.br/fhir/r4/StructureDefinition/BRCID10Avaliado-1.0](http://www.saude.gov.br/fhir/r4/StructureDefinition/BRCID10Avaliado-1.0)"
                                           ]
                        },
        "clinicalStatus": {
                        "coding": [
                                            {
                                            "system": "[http://terminology.hl7.org/CodeSystem/condition-clinical](http://terminology.hl7.org/CodeSystem/condition-clinical)",
                                            "code": "active"
                                            }
                                        ]
                        },
        "category": [
                                        {
                                        "coding": [
                                                                            {
                                                                            "system": "[http://www.saude.gov.br/fhir/r4/CodeSystem/BRCategoriaDiagnostico](http://www.saude.gov.br/fhir/r4/CodeSystem/BRCategoriaDiagnostico)",
                                                                            "code": "01",
                                                                            "display": "Principal"
                                                                            }
                                                                        ]
                                        }
                                    ],
        "code": {
                        "coding": [
                                            {
                                            "system": "[http://www.saude.gov.br/fhir/r4/CodeSystem/BRCID10](http://www.saude.gov.br/fhir/r4/CodeSystem/BRCID10)",
                                            "code": "I85"
                                            }
                                        ]
                        },
        "subject": {
                        "identifier": {
                                            "system": "[http://www.saude.gov.br/fhir/r4/StructureDefinition/BRIndividuo-1.0](http://www.saude.gov.br/fhir/r4/StructureDefinition/BRIndividuo-1.0)",
                                            "value": "{{meu_cns}}"
                                            }
                        },
        "note": [
                        {
                        "text" : "Varizes esofagianas sem sangramento."
                        }
                                    ]
      }
    }
  ]
}

```


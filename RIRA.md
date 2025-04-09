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


**FHIR Service**

```python
from .schemas import PatientRequestSchema, PatientResponseSchema
from db.conectaBanco import ConectaBanco
from fhir.resources.patient import Patient
from fhir.resources.humanname import HumanName
from fhir.resources.contactpoint import ContactPoint
from datetime import datetime

class FHIRService:
    def create_patient(self, request: dict, db: ConectaBanco):
        schema = PatientRequestSchema()
        
        # Validar e desserializar a solicitação
        request_data = schema.load(request)
        
        # Criar a entrada do paciente na tabela Patient usando o schema validado
        fhir_patient = Patient(
            resourceType=request_data["resourceType"],
            id=request_data.get("id"),
            identifier=request_data.get("identifier"),
            active=request_data.get("active"),
            name=[HumanName(**name) for name in request_data.get("name", [])],
            telecom=[ContactPoint(**telecom) for telecom in request_data.get("telecom", [])],
            gender=request_data.get("gender"),
            birthDate=request_data.get("birthDate"),
            deceasedBoolean=request_data.get("deceasedBoolean"),
            address=request_data.get("address"),
            contact=request_data.get("contact"),
            managingOrganization=request_data.get("managingOrganization")
        )

        # Converter o paciente FHIR para um dicionário e salvar no banco de dados
        patient_data = fhir_patient.dict()
        patient_id = db.criaRegistro("Patient", list(patient_data.keys()), list(patient_data.values()))

        # Lógica para salvar os nomes do paciente
        for name in request_data.get("name", []):
            name_data = {
                "patient_id": patient_id,
                "use": name.get("use"),
                "family": name.get("family"),
                "given": name.get("given")
            }
            db.criaRegistro("Name", list(name_data.keys()), list(name_data.values()))

        # Lógica para salvar os meios de telecomunicação
        for telecom in request_data.get("telecom", []):
            telecom_data = {
                "patient_id": patient_id,
                "system": telecom.get("system"),
                "value": telecom.get("value"),
                "use": telecom.get("use"),
                "rank": telecom.get("rank"),
                "period_end": telecom.get('period', {}).get('end') if telecom.get('period') else None
            }
            db.criaRegistro("Telecom", list(telecom_data.keys()), list(telecom_data.values()))

        return patient_data

    def get_patient(self, patient_id: str, db: ConectaBanco):
        # Recuperar dados do paciente e formatar a resposta
        query = f"SELECT * FROM Patient WHERE id='{patient_id}'"
        patient_data = db.consulta(query)
        if patient_data:
            # Recuperar e adicionar nomes
            query_names = f"SELECT * FROM Name WHERE patient_id='{patient_id}'"
            names = db.consulta(query_names)
            patient_data['name'] = names

            # Recuperar e adicionar telecomunicações
            query_telecom = f"SELECT * FROM Telecom WHERE patient_id='{patient_id}'"
            telecoms = db.consulta(query_telecom)
            patient_data['telecom'] = telecoms

            # Criar um objeto FHIR Patient a partir dos dados recuperados
            fhir_patient = Patient(**patient_data)
            schema = PatientResponseSchema()
            return schema.dump(fhir_patient.dict())
        return None

    def convert_to_fhir_patient(self, data):
        patient = Patient(
            id=data["id"],
            name=[HumanName(family=data["last_name"], given=[data["first_name"]])],
            gender=data["gender"],
            birthDate=datetime.strptime(data["birth_date"], "%Y-%m-%d").date().isoformat()
        )
        return patient.json()

# Exemplo de uso:
fhir = FHIRService()
# Supondo que data seja um dicionário com informações do paciente
data = {
    "id": "12345",
    "first_name": "John",
    "last_name": "Doe",
    "gender": "male",
    "birth_date": "1980-01-01"
}
fhir_patient = fhir.convert_to_fhir_patient(data)
print(fhir_patient)
```

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


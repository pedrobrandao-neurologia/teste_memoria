# M\@T Digital — Teste de Alteração da Memória

**Versão do app:** `MAT-Digital 1.0.4` · **Arquivo único:** `index.html`

> Implementação inteiramente em navegador do **Memory Alteration Test (M\@T)**, com interface responsiva, síntese de voz (TTS), registro estruturado de respostas, indicadores de qualidade e exportação em **PDF/JSON/CSV**. O objetivo é **rastreamento cognitivo** orientado a memória episódica e orientação temporal, **não** substituindo avaliação clínica formal.

---

## 1) Sumário executivo

Este repositório contém um aplicativo de página única (SPA) que executa o M\@T em português brasileiro (pt-BR). O fluxo contempla **codificação** (palavras/frases), **orientação temporal**, **conhecimento semântico**, **evocação livre** e **evocação com pistas**. O sistema usa **Web Speech API** para instruções faladas, controla **qualidade do teste** (p. ex., troca de aba, respostas supersônicas) e disponibiliza **relatório padronizado** ao final.

---

## 2) Principais recursos

* **Sem instalação/servidor**: basta abrir o `index.html` no navegador.
* **Interface responsiva (Tailwind via CDN)**, tipografia *Inter* e componentes com foco em legibilidade clínica.
* **Síntese de voz (pt-BR)** para instruções, perguntas e *feedback* imediato na fase de codificação.
* **Domínios avaliados e pontuação máxima**

  * Codificação (palavras/frases) … **10**
  * Orientação no tempo … **5**
  * Semântica (conhecimentos gerais) … **15**
  * Evocação livre … **10**
  * Evocação com pistas … **10**
  * **Total** … **50**
* **Exportação**:

  * **PDF** (relatório clínico sintético com tabela de domínios e alertas de qualidade),
  * **JSON** (estrutura completa para banco de dados),
  * **CSV** (tabela de domínios e escores).
* **Indicadores de Qualidade (QC)**:

  * Troca de aba durante a aplicação,
  * Respostas com latência < **200 ms** (padrão sinalizado como atípico).
* **Orientação dinâmica**: perguntas de dia da semana, mês, dia, ano e estação com correção automática **contextual** à data do teste (hemisfério sul).
* **Acessibilidade e usabilidade**: botões grandes, alto contraste e *feedback* visual de acertos/erros na codificação.

---

## 3) Como executar

1. Faça *download* do arquivo único (`index.html`) ou clone o repositório.
2. **Abra o arquivo no seu navegador** (recomendado: Chrome/Edge atuais).
3. Permita **áudio** do dispositivo para ouvir as instruções.
4. Clique em **Iniciar** → preencha **idade** e **escolaridade** → **Começar o teste**.

> ⚠️ **TTS (síntese de voz)** depende de vozes pt-BR disponíveis. No primeiro uso, o navegador pode demorar alguns segundos para carregar as vozes; a reprodução inicia após a primeira interação do usuário.

---

## 4) Estrutura do código (alto nível)

* **Dados do teste (`testData`)**: versão, parâmetros e *blocks* com itens.
* **Máquina de estados (`state`)**: *screen*, *block*, índices, respostas, escores, *flags* de qualidade e *timestamps*.
* **Renderização**: funções `render*` montam cada etapa; UI atualiza por substituição do `innerHTML`.
* **Lógica de prova**:

  * **Codificação** (palavras e frases + *quizzes*),
  * **Orientação temporal** (respostas geradas dinamicamente),
  * **Semântica** (múltipla escolha),
  * **Evocação livre** (seleção entre alvos e distratores),
  * **Evocação com pistas** (itens não lembrados).
* **Exportação**: `exportPDF()`, `exportJSON()`, `exportCSV()`.

---

## 5) Configuração e personalização

Parâmetros em `testData.config`:

```js
{
  word_presentation_ms: 1500,   // duração de exibição de palavras/frases
  isi_ms: 500,                   // intervalo entre estímulos
  free_recall_window_s: 20       // janela temporal nominal para evocação livre (UI baseada em seleção)
}
```

Você pode ajustar:

* **Tempos** de apresentação/inter-estímulo.
* **Itens** (palavras, frases, distratores) mantendo coerência semântica.
* **Textos de instrução** e *cues* para evocação com pistas.

> **Boas práticas**: ao modificar itens, preserve **equilíbrio de categorias**, **frequência lexical** e **dificuldade** para manter validade de conteúdo.

---

## 6) Escores e interpretação

O aplicativo calcula automaticamente:

* Escores por domínio e **pontuação total (0–50)**.
* **Evocação total** (soma de livre + pistas).
* *Flags* de qualidade (para interpretação contextual).

> **Importante**: Este software **não fornece pontos de corte normativos**. Interpretações clínicas devem considerar **idade, escolaridade e contexto clínico** (ex.: comorbidades, depressão, uso de fármacos, déficits sensoriais) e **referenciais normativos/locais** do M\@T quando aplicável.

---

## 7) Exportação de dados

### 7.1 PDF

Relatório com cabeçalho (data, duração, idade/escolaridade), tabela de domínios e **pontuação total**. Campos de **Alertas de Qualidade** são anexados quando presentes.

### 7.2 JSON (esquema)

```json
{
  "testVersion": "MAT-Digital 1.0.4",
  "testDate": "2025-09-24T00:00:00.000Z",
  "durationSeconds": 123,
  "profile": { "age": 72, "education": 8 },
  "scores": {
    "codificacao": { "score": 0, "max": 10 },
    "orientacao": { "score": 0, "max": 5 },
    "semantica": { "score": 0, "max": 15 },
    "recuperacao_livre": { "score": 0, "max": 10 },
    "recuperacao_pistas": { "score": 0, "max": 10 },
    "recuperacao_total": { "score": 0, "max": 10 },
    "total": { "score": 0, "max": 50 }
  },
  "qualityFlags": ["..."],
  "detailedAnswers": { "codificacao-8-0": "Cereja", "...": "..." },
  "recallDetails": [{ "cue": "Qual era a fruta?", "answer": "Cereja", "recalled": "free" }, "..."]
}
```

### 7.3 CSV

Tabela simples: `Dominio,Pontos,Maximo` (um por linha, mais a linha Total).

---

## 8) Requisitos e suporte

* **Navegadores**: Chrome/Edge/Firefox (recente). Safari funciona, mas TTS pode variar.
* **Dispositivos**: Desktop/notebook ou **tablet ≥10”**.
* **Permissões**: Áudio habilitado para TTS.
* **Offline**: Opera localmente; bibliotecas via CDN (configure *mirrors* locais se necessário em ambientes sem internet).

---

## 9) Segurança, privacidade e LGPD

* **Sem backend**: dados ficam **apenas em memória** do navegador até exportação explícita (PDF/JSON/CSV).
* **Identificadores pessoais**: por padrão, apenas **idade** e **escolaridade**. Se incluir identificadores, garanta **consentimento** e **padrões de segurança** locais.
* **LGPD/Ética**:

  * Defina base legal (consentimento/assistência à saúde/interesse legítimo institucional).
  * Minimize dados e aplique **criptografia** e **controle de acesso** nos arquivos exportados.

---

## 10) Qualidade e *compliance* metodológico

* **Controles de QC** automáticos (troca de aba; latências anômalas).
* **Apresentação padronizada** (tempos, ISI) e **feedback** apenas na fase de codificação para evitar pistas indevidas.
* **Evocação**: livre (seleção de alvos entre distratores) e, em seguida, **pistas** para itens remanescentes, refletindo a dissociação entre recuperação espontânea e facilitada.

---

## 11) Limitações conhecidas

* **Normas/cortes**: não inclusos; dependem de estudos locais e validações específicas.
* **TTS**: dependente de vozes pt-BR do SO/navegador.
* **Evocação livre**: operacionalizada por seleção em grade (não por produção oral/livre digitada), o que **padroniza registro** mas altera a *ecologia* da tarefa.

---

## 12) Diretrizes de uso clínico

* Utilize em **ambiente silencioso**, boa iluminação e **sem interrupções**.
* Confirme acuidade **auditiva/visual** adequadas e **alfabetização funcional**.
* Registre **medicações**, humor e queixas subjetivas de memória.
* Revise **flags de qualidade** antes de interpretar o escore total.

---

## 13) Personalização avançada

* **Itentário de itens**: edite `testData.blocks` (mantenha consistência semântica e dificuldade).
* **Distratores**: adapte listas para **equilíbrio de plausibilidade** e **frequência lexical**.
* **Idiomas**: altere *strings* e `utterance.lang` no TTS.
* **Relatório**: personalize `exportPDF()` (logo institucional, assinatura digital, QR de verificação, etc.).

---

## 14) Teste e validação

* **Testes manuais**:

  * Fluxo completo (todas as telas).
  * Exportações (abrir PDF/JSON/CSV) em diferentes SOs.
  * TTS: presença de voz pt-BR e sincronização com tempo de tela.
* **Ensaios pilotos**: avalie **confiabilidade teste-reteste**, **aceitabilidade** e **tempo total** de aplicação em sua população-alvo.

---

## 15) Licença e atribuição

Defina a licença conforme a política do seu serviço (por exemplo, **MIT** para o código e **CC BY-NC** para materiais de teste, se aplicável). Caso use versões **proprietárias** do M\@T ou traduções específicas, verifique **direitos autorais** e **permissões de uso**.

---

## 16) Referências e leituras recomendadas

Rami L, Mollica MA, García-Sanchez C, et al. The Memory Alteration Test (M@T) discriminates between subjective memory complaints, mild cognitive impairment and Alzheimer's disease. Arch Gerontol Geriatr. 2014;59(1):90-97.

Custodio N, Herrera-Perez E, Lira D, et al. The Memory Alteration Test discriminates between cognitively healthy status, mild cognitive impairment and Alzheimer's disease. Dement Geriatr Cogn Dis Extra. 2014;4(2):314-321.

Albert MS, DeKosky ST, Dickson D, et al. The diagnosis of mild cognitive impairment due to Alzheimer's disease: recommendations from the National Institute on Aging-Alzheimer's Association workgroups. Alzheimers Dement. 2011;7(3):270-279.
McKhann GM, Knopman DS, Chertkow H, et al. The diagnosis of dementia due to Alzheimer's disease: recommendations from the National Institute on Aging-Alzheimer's Association workgroups. Alzheimers Dement. 2011;7(3):263-269.
Petersen RC, Lopez O, Armstrong MJ, et al. Practice guideline update summary: mild cognitive impairment: report of the Guideline Development, Dissemination, and Implementation Subcommittee of the American Academy of Neurology. Neurology. 2018;90(3):126-135.

Ministério da Saúde. Identificação da Demência na Atenção Primária à Saúde (roteiro prático). Brasília: MS; 2024. (Documento oficial)

Schilling LP, Balthazar MLF, Radanovic M, et al. Diagnosis of Alzheimer’s disease: recommendations of the Scientific Department of Cognitive Neurology and Aging of the Brazilian Academy of Neurology. Dement Neuropsychol. 2022;16(3 Suppl 1):25-39. doi:10.1590/1980-5764-DN-2022-S102EN.

Smid J, Studart-Neto A, César-Freitas KG, et al. Declínio cognitivo subjetivo, comprometimento cognitivo leve e demência—diagnóstico sindrômico: recomendações do DC de Neurologia Cognitiva e do Envelhecimento da ABN. Dement Neuropsychol. 2022;16(3 Suppl 1):1-17.

---

## 17) Agradecimentos

Agradecemos às equipes clínicas e de pesquisa que contribuíram com *feedback* sobre usabilidade, acessibilidade e padronização dos relatórios.

---

## 18) Changelog (resumo)

* **1.0.4**: aprimoramentos de TTS, *flags* de qualidade e relatório PDF; revisão de distratores e fluxo de evocação.

---

## 19) Suporte e contato

Abra uma *issue* com: contexto do navegador/SO, *screenshots* do console (F12), passos para reproduzir o problema e arquivo de **JSON** exportado (se possível, anonimizado).

---

### Como citar (sugestão)

> *M\@T Digital (MAT-Digital v1.0.4): implementação de navegador do Memory Alteration Test, com TTS, indicadores de qualidade e exportação estruturada. Brasília; 2025. Disponível em: repositório GitHub do projeto.*

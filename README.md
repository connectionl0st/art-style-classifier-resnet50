# art-style-classifier-resnet50

Classificazione di dipinti per pittore (5 classi) usando ResNet50 con
Transfer Learning e patch aggregation.

Il problema: dato un dipinto, il modello deve riconoscere a quale pittore appartiene. Per farlo ho preso ResNet50 pre-addestrata su ImageNet e l'ho adattata tramite Transfer Learning.

Siccome ResNet50 accetta solo input 224×224, invece di ridimensionare brutalmente i quadri (perdendo dettagli, metodo resize-based) ho optato per il **patching**:
ogni dipinto viene suddiviso in patch **224×224**, ognuna classificata separatamente. I risultati delle patch vengono poi aggregati con 4 strategie diverse per dare il verdetto sull'intera opera.

Il lavoro si basa su un paper di riferimento che considera un numero maggiore di pittori — qui ci si è limitati a 5, ma il framework è identico: cambia solo il numero di classi in output.

**Classi:** Van Gogh · Picasso · Renoir · Degas · Dürer

## Dataset
| Split | Patch |
|-------|-------|
| Train | 32.247 |
| Val   | 10.806 |
| Test  | 10.300 |


## Pipeline
1. Data Augmentation sul training set (flip, rotazione, brightness, zoom)
2. Warm-Up — 10 epoche, LR 1e-3, solo la testa classificatrice
3. Fine-Tuning — 20 epoche, LR 1e-5, ultimi 30 layer di ResNet50 sbloccati
4. Patch Aggregation — confronto tra 4 metodi (Majority Voting,
   Probability Averaging, Max Pooling, Weighted Averaging)

## Risultati

| Pittore | Precision | Recall | F1 | Support |
|---|---|---|---|---|
| Dürer | 0.97 | 0.94 | 0.95 | 2.123 |
| Degas | 0.89 | 0.89 | 0.89 | 2.814 |
| Picasso | 0.55 | 0.88 | 0.68 | 796 |
| Renoir | 0.83 | 0.80 | 0.81 | 1.514 |
| Van Gogh | 0.90 | 0.79 | 0.84 | 3.053 |
| **Accuracy** | | | **0.86** | **10.300** |

Il caso più interessante è Picasso: precision bassa (0.55) ma recall alta (0.88). Il modello lo "trova" spesso, ma tende anche a scambiare altri pittori per lui. Ha senso — lo stile di Picasso (cubismoo) è tale che le patch a livelloo locale non sono sempre distinguibili da altri stili, e poi il dataset è quello con meno esempi (796 patch vs 3.053 di Van Gogh). Dürer all'opposto è quasi perfetto: le sue opere hanno uno stile grafico molto distinguibile, anche a livello di singola Patch.

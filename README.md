# art-style-classifier-resnet50
Multi-class (5) painting classification using ResNet50 w transfer learning and patch aggregation.
Partendo dal modello ResNet50 si è fatto Transfer Learning per adattare il modello al caso specifico: dire a quale pittore appartiene un determinato quadro. Per il problema d'esempio si sono considerati solo 5 pittori (Van Gogh, Picasso, Renoir, Degas, Durer) ma nel paper di riferimento se ne sono considerati di più; l'unica cosa che cambia è il numero di classi e la dimensione del dataset.
Inoltre ResNet prende in ingresso soltanto input di 224x224, ho optato per il patching cosi da far considerare i dettagli al modello.

## Dataset
- Train: 32.247 patch | Val: 10.806 | Test: 10.300
- Immagini suddivise in patch 224×224 px

## Pipeline
1. Data Augmentation sul training set
2. Warm-Up (10 epoche, LR=1e-3, solo la testa)
3. Fine-Tuning (20 epoche, LR=1e-5, ultimi 30 layer)
4. Aggregazione patch: 4 metodi confrontati

## Risultati

                       precision    recall  f1-score   support

                Durer       0.97      0.94      0.95      2123
          Edgar_Degas       0.89      0.89      0.89      2814
        Pablo_Picasso       0.55      0.88      0.68       796
Pierre-Auguste_Renoir       0.83      0.80      0.81      1514
     Vincent_van_Gogh       0.90      0.79      0.84      3053

             accuracy                           0.86     10300
            macro avg       0.83      0.86      0.83     10300
         weighted avg       0.87      0.86      0.86     10300

Come ci si poteva aspettare per Picasso il modello non funziona altrettanto bene come per Durer, questo perché col patching considero porzioni di quadro, e in molti casi Picasso non ha uno stile "inconfondibile" rispetto agli altri autori. 

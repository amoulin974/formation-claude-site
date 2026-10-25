# Un agent en cent lignes

*20 minutes.* En séance 1, vous avez vu la boucle d'un agent en pseudo-code. Vous allez maintenant la lire, et la modifier, en vrai. Le code est dans le dépôt d'exercices : `agent/mini_agent.py`.

## Avant de commencer

L'API se paie à l'usage, avec un compte sur la [console Claude](https://platform.claude.com), **distinct de votre abonnement Claude**. Créez une clé, fixez un plafond de dépense bas dans la console, puis :

```bash
make agent                              # environnement Python et SDK
export ANTHROPIC_API_KEY=sk-ant-...     # ne jamais committer cette clé
agent/.venv/bin/python agent/mini_agent.py "Les tests C++ passent-ils ?"
```

Les tests de l'agent, eux, n'ont besoin d'aucune clé : `make test-agent` utilise un faux client.

!!! warning "Piège"
    Une clé d'API donne accès à votre compte de facturation. Elle se met dans une variable d'environnement, jamais dans le code, un fichier versionné ou une demande à un agent.

## Les trois ingrédients

### 1. Des outils décrits au modèle

Chaque outil est décrit par un nom, une description en langage naturel et un schéma JSON de ses paramètres. C'est **tout** ce que le modèle sait de l'outil.

```python
{
    "name": "lire_fichier",
    "description": "Lit un fichier texte du dépôt et renvoie son contenu.",
    "input_schema": {
        "type": "object",
        "properties": {
            "chemin": {"type": "string", "description": "Chemin relatif à la racine du dépôt"}
        },
        "required": ["chemin"],
    },
}
```

### 2. Du code qui exécute les outils

Le modèle n'exécute rien. Il **demande** un appel d'outil ; c'est votre code qui décide de l'exécuter, et comment. C'est là que se trouve la sécurité : le mini-agent refuse de lire hors du dépôt et ne lance que trois commandes de test, fixées à l'avance.

```python
if nom == "lire_fichier":
    chemin = (RACINE / entree.get("chemin", "")).resolve()
    if not chemin.is_relative_to(RACINE):
        return "Refusé : le fichier doit se trouver dans le dépôt."
```

### 3. La boucle

```python
messages = [{"role": "user", "content": demande}]
for _ in range(max_tours):
    reponse = client.messages.create(
        model=MODELE, max_tokens=4096, system=SYSTEME, tools=OUTILS, messages=messages,
    )
    messages.append({"role": "assistant", "content": reponse.content})

    if reponse.stop_reason != "tool_use":          # le modèle a fini
        return "".join(b.text for b in reponse.content if b.type == "text")

    resultats = []
    for bloc in reponse.content:
        if bloc.type == "tool_use":                  # le modèle demande un outil
            resultats.append({
                "type": "tool_result",
                "tool_use_id": bloc.id,
                "content": executer_outil(bloc.name, bloc.input),
            })
    messages.append({"role": "user", "content": resultats})
```

Remarquez que **tout l'historique est renvoyé à chaque appel** : c'est la fenêtre de contexte de la séance 1, que vous voyez grossir sous vos yeux. Claude Code fait la même chose, avec beaucoup plus d'outils, des permissions, de la compaction, des sous-agents…

!!! tip "À retenir"
    Un agent, c'est un modèle, des outils, et une boucle. Toute la valeur d'un outil comme Claude Code tient dans la qualité de ses outils, de ses garde-fous et de la gestion du contexte.

## Exercice

!!! example "Exercice : étendre le mini-agent"
    Avec Claude Code, sur le dépôt d'exercices :

    1. Ajoutez un outil `ecrire_fichier(chemin, contenu)` qui **demande confirmation** dans le terminal avant d'écrire. Vous venez d'implémenter les permissions.
    2. Ajoutez un test unitaire avec le faux client : un refus de l'utilisateur doit être renvoyé au modèle comme résultat d'outil.
    3. Donnez à l'agent la tâche « Corrige le bug du classement signalé au TP 1 » sur une copie non corrigée du dépôt. Comptez les tours et observez ses choix.
    4. Pour comparer : la même tâche avec Claude Code. Qu'est-ce qui explique l'écart ?

??? success "Piste pour la question 1"
    ```python
    if nom == "ecrire_fichier":
        chemin = (RACINE / entree["chemin"]).resolve()
        if not chemin.is_relative_to(RACINE):
            return "Refusé : le fichier doit se trouver dans le dépôt."
        print(f"\nL'agent veut écrire {entree['chemin']} ({len(entree['contenu'])} caractères).")
        if input("Autoriser ? [o/N] ").strip().lower() != "o":
            return "Refusé par l'utilisateur."
        chemin.write_text(entree["contenu"], encoding="utf-8")
        return f"Fichier écrit : {entree['chemin']}"
    ```

    Pour le test, remplacez `input` par une fonction injectée en paramètre, comme le client.

## Pour aller plus loin

- [Utilisation d'outils avec l'API](https://docs.claude.com/en/docs/agents-and-tools/tool-use/overview)
- Le [SDK Agent](https://code.claude.com/docs/en/agent-sdk/overview) : le moteur de Claude Code (outils, permissions, contexte), utilisable dans vos propres programmes.

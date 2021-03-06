{% tip %}

**Tips**:
- Wenn erforderliche Reviews aktiviert sind und ein Mitarbeiter mit Zugriffsberechtigung _write_ (Schreiben), _admin_ (Administrator) oder _owner_ (Inhaber) auf das Repository einen Review mit Anforderungen für Änderungen absendet, kann der Pull Request erst zusammengeführt werden, wenn derselbe Mitarbeiter einen anderen Review absendet, der die Änderungen in dem Pull Request genehmigt.
- Repository-Inhaber und -Administratoren können einen Pull Request sogar dann zusammenführen, wenn er keinen genehmigenden Review erhalten hat oder wenn ein Reviewer, der die Änderungen verlangt hat, die Organisation verlassen hat oder nicht verfügbar ist.
- Wenn sowohl erforderliche Reviews wie das Verwerfen veralteter Reviews aktiviert sind und ein den Code verändernder Commit an den Branch eines genehmigten Pull Requests übermittelt wird, dann wird die Genehmigung verworfen. Der Pull Request muss erneut überprüft und genehmigt werden, bevor er zusammengeführt werden kann.
- Wenn verschiedene offene Pull Requests jeweils einen Head-Branch aufweisen, der auf denselben Commit verweist, kannst Du sie nicht zusammenführen, wenn der Review bei einer oder bei beiden ausstehend ist oder abgelehnt wurde.
- If your repository requires approving reviews from people with write or admin permissions, then any approvals from people with these permissions are denoted with a green check mark, and approvals from people without these permissions have a gray check mark. Approvals with a gray check mark do not affect whether the pull request can be merged.
- Pull request authors cannot approve their own pull requests.

{% endtip %}

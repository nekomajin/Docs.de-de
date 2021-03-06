---
title: Hinzufügen der Suche zu Razor-Seiten in ASP.NET Core
author: rick-anderson
description: Informationen zum Hinzufügen der Suche zu ASP.NET Core Razor Pages
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 05/30/2018
uid: tutorials/razor-pages/search
ms.openlocfilehash: 14b3b501915a22aedbc10bd7bc8e5eef408f185c
ms.sourcegitcommit: a1afd04758e663d7062a5bfa8a0d4dca38f42afc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36278147"
---
# <a name="add-search-to-aspnet-core-razor-pages"></a>Hinzufügen der Suche zu Razor-Seiten in ASP.NET Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

In diesem Dokument wird der Indexseite die Suchfunktion hinzugefügt, die das Suchen von Filmen nach *Genre* oder *Namen* ermöglicht.

Aktualisieren Sie die `OnGetAsync`-Methode der Indexseite mit folgendem Code:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

Die erste Zeile der `OnGetAsync`-Methode erstellt eine [LINQ](/dotnet/csharp/programming-guide/concepts/linq/)-Abfrage zum Auswählen der Filme:

```csharp
var movies = from m in _context.Movie
             select m;
```

Die Abfrage wird an dieser Stelle *nur* definiert und **nicht** auf die Datenbank angewendet.

Wenn der `searchString`-Parameter eine Zeichenfolge enthält, wird die Filmabfrage so geändert, dass die Suchzeichenfolge gefiltert wird:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

Der Code `s => s.Title.Contains()` ist ein [Lambdaausdruck](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Lambdaausdrücke werden in methodenbasierten [LINQ](/dotnet/csharp/programming-guide/concepts/linq/)-Abfragen als Argumente für standardmäßige Abfrageoperatormethoden wie die [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq)-Methode oder `Contains` verwendet (siehe den vorangehenden Code). LINQ-Abfragen werden nicht ausgeführt, wenn sie definiert oder durch Aufrufen einer Methode geändert werden (z.B. `Where`, `Contains` oder `OrderBy`). Stattdessen wird die Ausführung der Abfrage verzögert. Dies bedeutet, dass die Auswertung eines Ausdrucks so lange hinausgezögert wird, bis dessen realisierter Wert durchlaufen oder die `ToListAsync`-Methode aufgerufen wird. Weitere Informationen finden Sie unter [Abfrageausführung](/dotnet/framework/data/adonet/ef/language-reference/query-execution).

**Hinweis:** Die [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains)-Methode wird in der Datenbank und nicht im C#-Code ausgeführt. Die Groß-/Kleinschreibung in der Abfrage hängt von der Datenbank und Sortierung ab. In SQL Server wird `Contains` zu [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql) zugeordnet, das Groß-/Kleinschreibung nicht beachtet. In SQLite wird bei der Standardsortierung Groß-/Kleinschreibung beachtet.

Navigieren Sie zur Seite „Movies“, und fügen Sie eine Abfragezeichenfolge wie z.B. `?searchString=Ghost` an die URL an (z.B. `http://localhost:5000/Movies?searchString=Ghost`). Die gefilterten Filme werden angezeigt.

![Indexansicht](search/_static/ghost.png)

Wenn die Routenvorlage der Indexseite hinzugefügt wird, kann die Suchzeichenfolge als URL-Segment (z.B. `http://localhost:5000/Movies/Ghost`) übergeben werden.

```cshtml
@page "{searchString?}"
```

Die vorangehende Routeneinschränkung ermöglicht das Suchen des Titels als Routendaten (URL-Segment) anstatt als Wert einer Abfragezeichenfolge.  Das `?` in `"{searchString?}"` bedeutet, dass dies ein optionaler Routenparameter ist.

![Indexansicht mit dem der URL hinzugefügten Wort „ghost“ und einer zurückgegebenen Filmliste mit zwei Filmen: Ghostbusters und Ghostbusters 2](search/_static/g2.png)

Sie können jedoch von den Benutzern nicht erwarten, dass sie die URL ändern, um nach einem Film zu suchen. In diesem Schritt wird eine Benutzeroberflächenoption hinzugefügt, um Filme zu filtern. Wenn Sie die Routeneinschränkung `"{searchString?}"` hinzugefügt haben, entfernen Sie sie.

Öffnen Sie die Datei *Pages/Movies/Index.cshtml*, und fügen Sie im folgenden Code das hervorgehobene `<form>`-Markup hinzu:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index2.cshtml?highlight=14-19&range=1-22)]

Das HTML-Tag `<form>` verwendet das [Hilfsprogramm für Formulartags](xref:mvc/views/working-with-forms#the-form-tag-helper). Bei Übermittlung des Formulars wird die Filterzeichenfolge an die Seite *Pages/Movies/Index* gesendet. Speichern Sie die Änderungen, und testen Sie den Filter.

![Indexansicht mit dem in das Filtertextfeld eingegebenen Wort „ghost“](search/_static/filter.png)

## <a name="search-by-genre"></a>Suche nach Genre

Fügen Sie *Pages/Movies/Index.cshtml.cs* folgende hervorgehobene Eigenschaften hinzu:

::: moniker range="= aspnetcore-2.0"
[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1"
[!code-csharp[](razor-pages-start/sample/RazorPagesMovie21/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]
::: moniker-end


`SelectList Genres` enthält die Liste der Genres. Dies ermöglicht dem Benutzer, ein Genre in der Liste auszuwählen.

Die `MovieGenre`-Eigenschaft enthält das spezifische Genre, das der Benutzer wählt (z.B. Western).

Aktualisieren Sie die `OnGetAsync`-Methode mit folgendem Code:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

Der folgende Code ist eine LINQ-Abfrage, die alle Genres aus der Datenbank abruft.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

Die `SelectList` von Genres wird durch Projizieren der unterschiedlichen Genres erstellt.

<!-- BUG in OPS
Tag snippet_selectlist's start line '75' should be less than end line '29' when resolving "[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]"

There's no start line.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]
-->

```csharp
Genres = new SelectList(await genreQuery.Distinct().ToListAsync());
```

### <a name="adding-search-by-genre"></a>Hinzufügen einer Suche nach Genre

Aktualisieren Sie *Index.cshtml* wie folgt:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

Testen Sie die App mit einer Suche nach Genre, Filmtitel und beidem.

> [!div class="step-by-step"]
> [Zurück: Aktualisieren der Seiten](xref:tutorials/razor-pages/da1)
> [Weiter: Hinzufügen eines neuen Felds](xref:tutorials/razor-pages/new-field)

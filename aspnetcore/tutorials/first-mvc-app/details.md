---
title: "Untersuchen der Methoden „Details“ und „Delete“"
author: rick-anderson
description: "Die Controllermethode „Details“ und Ansicht in einer einfachen ASP.NET Core MVC-App."
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 03/07/2017
ms.topic: get-started-article
ms.assetid: 870192b4-8d4f-45c7-8c14-83d02bc0ad79
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-mvc-app/details
ms.openlocfilehash: bab93a2faa122d9d6d2e71367519baa09bd76bd1
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/11/2017
---
# <a name="examining-the-details-and-delete-methods"></a><span data-ttu-id="7ff78-104">Untersuchen der Methoden „Details“ und „Delete“</span><span class="sxs-lookup"><span data-stu-id="7ff78-104">Examining the Details and Delete methods</span></span>

<span data-ttu-id="7ff78-105">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7ff78-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="7ff78-106">Öffnen Sie den „Movie“-Controller, und untersuchen Sie die `Details`-Methode.</span><span class="sxs-lookup"><span data-stu-id="7ff78-106">Open the Movie controller and examine the `Details` method:</span></span>

<span data-ttu-id="7ff78-107">[!code-csharp[Main](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_details)]</span><span class="sxs-lookup"><span data-stu-id="7ff78-107">[!code-csharp[Main](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_details)]</span></span>

<span data-ttu-id="7ff78-108">Das MVC-Gerüstbaumodul, das diese Aktionsmethode erstellt hat, fügt einen Kommentar mit einer HTTP-Anforderung hinzu, die die Methode aufruft.</span><span class="sxs-lookup"><span data-stu-id="7ff78-108">The MVC scaffolding engine that created this action method adds a comment showing an HTTP request that invokes the method.</span></span> <span data-ttu-id="7ff78-109">In diesem Fall ist dies eine GET-Anforderung mit drei URL-Segmenten: dem Controller `Movies`, der Methode `Details` und dem Wert von `id`.</span><span class="sxs-lookup"><span data-stu-id="7ff78-109">In this case it's a GET request with three URL segments, the `Movies` controller, the `Details` method and an `id` value.</span></span> <span data-ttu-id="7ff78-110">Wie bereits erwähnt, werden diese Segmente in *Startup.cs* definiert.</span><span class="sxs-lookup"><span data-stu-id="7ff78-110">Recall these segments are defined in *Startup.cs*.</span></span>

<span data-ttu-id="7ff78-111">[!code-csharp[Main](start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]</span><span class="sxs-lookup"><span data-stu-id="7ff78-111">[!code-csharp[Main](start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]</span></span>

<span data-ttu-id="7ff78-112">EF erleichtert das Suchen nach Daten mithilfe der `SingleOrDefaultAsync`-Methode.</span><span class="sxs-lookup"><span data-stu-id="7ff78-112">EF makes it easy to search for data using the `SingleOrDefaultAsync` method.</span></span> <span data-ttu-id="7ff78-113">Ein wichtiges in die Methode integriertes Sicherheitsmerkmal ist, dass der Code überprüft, ob die Suchmethode einen Film gefunden hat, bevor sie versucht, etwas damit zu tun.</span><span class="sxs-lookup"><span data-stu-id="7ff78-113">An important security feature built into the method is that the code verifies that the search method has found a movie before it tries to do anything with it.</span></span> <span data-ttu-id="7ff78-114">Ein Hacker kann beispielsweise Fehler in die Website einschleusen, indem die von den Links erstellte URL von `http://localhost:xxxx/Movies/Details/1` in etwas wie `http://localhost:xxxx/Movies/Details/12345` geändert wird (oder einen anderen Wert, der keinen tatsächlichen Film darstellt).</span><span class="sxs-lookup"><span data-stu-id="7ff78-114">For example, a hacker could introduce errors into the site by changing the URL created by the links from `http://localhost:xxxx/Movies/Details/1` to something like  `http://localhost:xxxx/Movies/Details/12345` (or some other value that doesn't represent an actual movie).</span></span> <span data-ttu-id="7ff78-115">Wenn keine Prüfung auf einen NULL-Film erfolgen würde, würde die App eine Ausnahme auslösen.</span><span class="sxs-lookup"><span data-stu-id="7ff78-115">If you did not check for a null movie, the app would throw an exception.</span></span>

<span data-ttu-id="7ff78-116">Untersuchen Sie die Methoden `Delete` und `DeleteConfirmed`.</span><span class="sxs-lookup"><span data-stu-id="7ff78-116">Examine the `Delete` and `DeleteConfirmed` methods.</span></span>

<span data-ttu-id="7ff78-117">[!code-csharp[Main](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete)]</span><span class="sxs-lookup"><span data-stu-id="7ff78-117">[!code-csharp[Main](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete)]</span></span>

<span data-ttu-id="7ff78-118">Beachten Sie, dass die `HTTP GET Delete`-Methode den angegebenen Film nicht löscht, sondern eine Ansicht des Films zurückgibt, in der Sie den Löschvorgang (HttpPost) auslösen können.</span><span class="sxs-lookup"><span data-stu-id="7ff78-118">Note that the `HTTP GET Delete` method doesn't delete the specified movie, it returns a view of the movie where you can submit (HttpPost) the deletion.</span></span> <span data-ttu-id="7ff78-119">Das Ausführen eines Löschvorgangs als Reaktion auf eine GET-Anforderung (oder eigentlich eines Bearbeitungs-, Erstellungs- oder sonstigen Vorgangs, der Daten ändern) stellt eine Sicherheitslücke dar.</span><span class="sxs-lookup"><span data-stu-id="7ff78-119">Performing a delete operation in response to a GET request (or for that matter, performing an edit operation, create operation, or any other operation that changes data) opens up a security hole.</span></span>

<span data-ttu-id="7ff78-120">Die `[HttpPost]`-Methode, die die Daten löscht, heißt `DeleteConfirmed`, um der HTTP-POST-Methode eine eindeutige Signatur bzw. einen eindeutigen Namen zu geben.</span><span class="sxs-lookup"><span data-stu-id="7ff78-120">The `[HttpPost]` method that deletes the data is named `DeleteConfirmed` to give the HTTP POST method a unique signature or name.</span></span> <span data-ttu-id="7ff78-121">Die beiden Methodensignaturen werden nachstehend gezeigt:</span><span class="sxs-lookup"><span data-stu-id="7ff78-121">The two method signatures are shown below:</span></span>

<span data-ttu-id="7ff78-122">[!code-csharp[Main](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete2)]</span><span class="sxs-lookup"><span data-stu-id="7ff78-122">[!code-csharp[Main](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete2)]</span></span>

<span data-ttu-id="7ff78-123">[!code-csharp[Main](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete3)]</span><span class="sxs-lookup"><span data-stu-id="7ff78-123">[!code-csharp[Main](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete3)]</span></span>


<span data-ttu-id="7ff78-124">Die Common Language Runtime (CLR) erfordert überladene Methoden, um eine eindeutige Parametersignatur zu erhalten (selber Methodenname, aber unterschiedliche Liste von Parametern).</span><span class="sxs-lookup"><span data-stu-id="7ff78-124">The common language runtime (CLR) requires overloaded methods to have a unique parameter signature (same method name but different list of parameters).</span></span> <span data-ttu-id="7ff78-125">Hier benötigen Sie jedoch zwei `Delete`-Methoden (eine für GET und eine für POST), die beide die gleiche Parametersignatur haben.</span><span class="sxs-lookup"><span data-stu-id="7ff78-125">However, here you need two `Delete` methods -- one for GET and one for POST -- that both have the same parameter signature.</span></span> <span data-ttu-id="7ff78-126">(Beide müssen eine einzelne ganze Zahl als Parameter akzeptieren.)</span><span class="sxs-lookup"><span data-stu-id="7ff78-126">(They both need to accept a single integer as a parameter.)</span></span>

<span data-ttu-id="7ff78-127">Es gibt zwei Ansätze zur Lösung dieses Problems. Eine besteht darin, die Methoden unterschiedlich zu benennen.</span><span class="sxs-lookup"><span data-stu-id="7ff78-127">There are two approaches to this problem, one is to give the methods different names.</span></span> <span data-ttu-id="7ff78-128">Dafür das der Gerüstbaumechanismus im vorherigen Beispiel gesorgt.</span><span class="sxs-lookup"><span data-stu-id="7ff78-128">That's what the scaffolding mechanism did in the preceding example.</span></span> <span data-ttu-id="7ff78-129">Dies bringt jedoch ein kleines Problem mit sich: ASP.NET ordnet Segmente einer URL anhand des Namens zu Aktionsmethoden zu. Wenn Sie die Methode umbenennen sollten, ist das Routing normalerweise nicht in der Lage, diese Methode zu finden.</span><span class="sxs-lookup"><span data-stu-id="7ff78-129">However, this introduces a small problem: ASP.NET maps segments of a URL to action methods by name, and if you rename a method, routing normally wouldn't be able to find that method.</span></span> <span data-ttu-id="7ff78-130">Die Lösung besteht (wie im Beispiel) im Hinzufügen des `ActionName("Delete")`-Attributs zur `DeleteConfirmed`-Methode.</span><span class="sxs-lookup"><span data-stu-id="7ff78-130">The solution is what you see in the example, which is to add the `ActionName("Delete")` attribute to the `DeleteConfirmed` method.</span></span> <span data-ttu-id="7ff78-131">Dieses Attribut führt die Zuordnung für das Routingsystem durch, sodass eine URL, die „/Delete/“ für eine POST-Anforderung enthält, die `DeleteConfirmed`-Methode findet.</span><span class="sxs-lookup"><span data-stu-id="7ff78-131">That attribute performs mapping for the routing system so that a URL that includes /Delete/ for a POST request will find the `DeleteConfirmed` method.</span></span>

<span data-ttu-id="7ff78-132">Eine weitere gängige Behelfslösung für Methoden mit identischen Namen und Signaturen ist das künstliche Ändern der POST-Methode durch Hinzufügen eines zusätzlichen (nicht verwendeten) Parameters.</span><span class="sxs-lookup"><span data-stu-id="7ff78-132">Another common work around for methods that have identical names and signatures is to artificially change the signature of the POST method to include an extra (unused) parameter.</span></span> <span data-ttu-id="7ff78-133">Siehe dazu die vorherige POST-Anforderung, der wir den `notUsed`-Parameter hinzugefügt haben.</span><span class="sxs-lookup"><span data-stu-id="7ff78-133">That's what we did in a previous post when we added the `notUsed` parameter.</span></span> <span data-ttu-id="7ff78-134">Sie können dasselbe hier für die `[HttpPost] Delete`-Methode ausführen:</span><span class="sxs-lookup"><span data-stu-id="7ff78-134">You could do the same thing here for the `[HttpPost] Delete` method:</span></span>

```csharp
// POST: Movies/Delete/6
[ValidateAntiForgeryToken]
public async Task<IActionResult> Delete(int id, bool notUsed)
```

<span data-ttu-id="7ff78-135">Vielen Dank für Ihr Interesse an dieser Einführung in ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="7ff78-135">Thanks for completing this introduction to ASP.NET Core MVC.</span></span> <span data-ttu-id="7ff78-136">Wir freuen uns über Kommentare, die Sie hinterlassen.</span><span class="sxs-lookup"><span data-stu-id="7ff78-136">We appreciate any comments you leave.</span></span> <span data-ttu-id="7ff78-137">[Erste Schritte mit MVC und EF Core](xref:data/ef-mvc/intro) ist ein ausgezeichneter Anschlussartikel an dieses Tutorial.</span><span class="sxs-lookup"><span data-stu-id="7ff78-137">[Getting started with MVC and EF Core](xref:data/ef-mvc/intro) is an excellent follow up to this tutorial.</span></span>

>[!div class="step-by-step"]
[<span data-ttu-id="7ff78-138">Zurück</span><span class="sxs-lookup"><span data-stu-id="7ff78-138">Previous</span></span>](validation.md)
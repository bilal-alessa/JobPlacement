Python and Django Live Project

Introduction

During the past two weeks, I had been working on using mostly Python and the Django framework to assist with both frontend and backend web development attempting to deploy a range of applications designed to be part of a larger application. Within the team I was assigned the primary task to design and impliment my own custom app to offer a unique experience to the user. My goal in this project was to create an app that would store a database of seafood related products with specific information for users to search through with the ability to edit or delete entries on request.


<h2>Core Features</h2>
<ul>
<li>Creating the Model</li>
<li>Storing Items into the Database</li>
</ul>

<h3>Creating The Model</h3>

The model would be the class I would instanciate and reference the majority of the time as it's the primary point of the application and what the back end would be founded upon. My main goal was to include the main applications requirments as to what would be logged into the database over time. With seafood, it would contain these primary points; The name, origins, harvest dates, and the difference between farmed or wild.

```
  from django.db import models
# Creating my class model and adding my objects manager at the bottom
WILD_FARMED_CHOICES = (
    ('Wild', 'Wild'),
    ('Farmed', 'Farmed')
)


class Seafood(models.Model):
    name = models.CharField(max_length=50, null=False)
    location = models.CharField(max_length=100)
    harvested_during = models.DateField(blank=True)
    wild_caught = models.CharField(max_length=15, choices=WILD_FARMED_CHOICES)

    SeafoodIndex = models.Manager()

    def __str__(self):
        return self.name
```

<h3>Storing Items in the Database</h3>

Storing anything in a database application is vital in that designing it correctly with accuracy and simplicity to avoid any missing information. The method I used was to bring in Django's ModelForms as they're better suited for instantiating a class from a model that has already been created and I can call on the models manager to access the table easily. With these few functions, I was able to add and edit from the database through the web application.

```
def seafoodadd(request):
    addseafoodform = SeafoodForm(data=request.POST or None)
    if request.method == 'POST' and addseafoodform.is_valid() and 'Add_Seafood' in request.POST:
        addseafoodform.save()
        return redirect('seafoodadd')
    content = {'addseafoodform': addseafoodform}
    print(Seafood.wild_caught)
    return render(request, 'SeafoodApp/SeafoodApp_add.html', content)


def edit_seafood(request, pk):
    seafood = get_object_or_404(Seafood, pk=pk)
    form = SeafoodForm(instance=seafood)

    if request.method == 'POST':
        form = SeafoodForm(request.POST, instance=seafood)
        if form.is_valid():
            form2 = form.save(commit=False)
            form2.save()
            return redirect('SeafoodApp_details', pk=seafood.pk)

    context = {'seafood': seafood, 'form': form}
    return render(request, 'SeafoodApp/SeafoodApp_edit.html', context)
```

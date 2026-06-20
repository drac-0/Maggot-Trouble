i have to dedicate a single note file (or even more, let's see it later) to talk about this topic.

it's a common shared experience between human, when you laugh at how dumb you are in the past. I am currently experiencing this.

I used to think the filecollector algorithm i made for linux machine (or anything system operation that adopt the single tree folder structure) is my magnum opus. Proud of it like how father proud of his son *OOT:never have experienced this*. Until i reexamine it after my overall skill improving

Let me show you the code first

```
import os

a = "/home"
os.chdir(a)
user = os.listdir() #collect user folder

folpath = [] #where to store user folder content path
filepath = [] 


def filecol(fdlist, fol1, file) :
	for path in fdlist :
		os.chdir(path) 
		content2 = os.listdir()
		for item in content2 :
			if os.path.isfile(item) :
				file.append(os.path.abspath(item))

			else :
				folderpath = os.path.abspath(item)
				fol1.append(folderpath)

# maybe i should assemble both of the filecol func, but idk how :P

def filecol2(folpath,filepath) :
	for folder in folpath :
		child = os.listdir(folder)
		if len(child) == 0 : #remove empty folder from list
			folpath.remove(folder)
			print(folder, "removed")

		for content in child :
			abpath = f'{os.path.abspath(folder)}/{content}'

			if "filecol.py" in abpath :
				print("DONT MESS WITH IT")
				continue

			if os.path.isfile(abpath) :
				filepath.append(abpath)

			else :
				folpath.append(abpath)

#i think the reason why i don't need a recursive function is because i append the folder path and it makes everysingle folder in this machine would be added 
#how lucky i am :DD

#unnecessary write check

filecol(user, folpath,filepath)

filecol2(folpath,filepath)

print(folpath)
print(filepath)
print("panjang folpath",len(folpath))
print("panjang filepath", len(filepath))
```

thank god through the dearest time machine for code. GIT. i am able to access this file. Thanks for myself also to have some nerve showing this dumbness.

So the specific part i really want to talk about. No, that doesn't sit right. I will go like this

So the specific part i really want to laugh about is this part
```
def filecol(fdlist, fol1, file) :
	for path in fdlist :
		os.chdir(path) 
		content2 = os.listdir()
		for item in content2 :
			if os.path.isfile(item) :
				file.append(os.path.abspath(item))

			else :
				folderpath = os.path.abspath(item)
				fol1.append(folderpath)

# maybe i should assemble both of the filecol func, but idk how :P

def filecol2(folpath,filepath) :
	for folder in folpath :
		child = os.listdir(folder)
		if len(child) == 0 : #remove empty folder from list
			folpath.remove(folder)
			print(folder, "removed")

		for content in child :
			abpath = f'{os.path.abspath(folder)}/{content}'

			if (descriptor) in abpath :
				print("DONT MESS WITH IT")
				continue

			if os.path.isfile(abpath) :
				filepath.append(abpath)

			else :
				folpath.append(abpath)
```

i am obligated to explain it first. We will walk through the first function

filecol(). In the beginning of the code, before this function. We have change the current directory from ./ to /home, therefore we can see, all the user folder. The purpose is clear, to collect all the user folder so later we can pass it through the filecol2() function.

Move to the next function which is filecol2(). You can see it takes 2 argument, folpath and filepath. The first thing its does is iterating over folpath list, if a folder empty remove it from the list. WOW, 5 line into the function we already got a violation of a universal list rule. Don't change a list if you iterate over it, its resulting in an inaccurate result for this case.

Fuck, back to explanation of code, we have nested loop that goes iterating the child folder inside our current folder we are iterating in main loop. Inside that loop, we create a local variable name abpath (stands for absolute path), concatinating the path of parent folder and the child item, whether its file or folder, what we do next will be depended on this variable. 

Got into the check statement, first it checks, if the descriptor script are the one we currently iterating over. If it is, then we will skip it and move along to next item. second check is to see, is the item file or folder. If its folder then, append it to folpath; if its file, then append it to filepath.

Alright, we done with explanation, now we can start point out the laughable parts of this code.

First thing first, its totally unnecessary to use 2 function. I can just use filecol2(), pass the list of user directory and it works fine. Shit is redundant.

The second one is the list violation. There is an unwritten rule for list iteration, it goes like this 'never update a list when you are iterating over it'. My idiotic mind says fuck that shit and decide to delete the empty folder when i am iterating over a folpath. 

'Oh for the sake of optimization let's get rid of a empty directory, therefore we cost no performance to later check and empty folder'. That's idea cost me an invalid result.

The third point, i am not actually sure about this yet, but my way to create a full directory of an item with format string is kind a.... weird. I read a lot of string handling code, and nobody does it that way. For now i haven't decide whether its good or bad. I will leave this point hanging for now

The solution to all of that problem would be like this:
```
import os
from time import perf_counter

home = "/home"
os.chdir(home)
folpath = os.listdir();
filepath = []

def filecol2(folpath, filepath):
    try:
        for folder in folpath:          
            child = os.listdir(folder)
            for content in child:
                #trash concat
                abpath = f'{os.path.abspath(folder)}/{content}'
                if os.path.isfile(abpath):
                    filepath.append(abpath)
                elif os.path.isdir(abpath):
                    folpath.append(abpath)
                else:
                    continue
    except Exception as e:
        print(e)

end = perf_counter()
filecol2(folpath,filepath)
print(len(filepath))
print(total)

```

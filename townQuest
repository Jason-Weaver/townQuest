#!/usr/local/bin/python3
import sys, cmd, os
from enum import Enum

# Implement
# 1. Buying per Person
# 2. The ability to have things cost other things
# 3. Change The Theif Guild to 
# 4. Have Room Hours kick people out
# 5. Put this on a Git Hub

# Constant Variable Keys used to quicken debugging ######################################
# Room keys
DESC 	= 'description'
GROUND	= 'ground'
SELL	= 'sell'
PEOPLE = 'people'
ITEMSNEEDEDTOENTER = 'items needed to enter'
ROOMISENTERABLE = 'room is enterable'
ROOMHOURS = 'room hours'
SHOPHOURS = 'shop hours'
NORTH 	= 'north'
SOUTH	= 'south'
WEST 	= 'west'
EAST 	= 'east'
UP 	= 'up'
DOWN	= 'down'

# Item Keys
GROUNDDESC = 'ground description'
SHORTDESC = 'short description'
LONGDESC = 'long description'
TAKEABLE = 'takeable'
DROPABLE = 'dropable'
EDIBLE	= 'edible'
DRINKABLE = 'drinkable'
SLEEPABLE = 'sleepable'
DESCWORDS = 'desciption words'

# SELL sub-Items
PRICE = 'price'
ITEMSINSTOCK = 'items in stock'

# People
DIALOG = 'dialog'
MAKEITEMSTAKEABLEAFTERDIALOG = 'make items takeable after dialog'


# GLOBAL VARIABLES ######################################################################
location = 'The Town Square' # initial position
purse = 24
inventory = ['README', 'map', 'donut', 'purse']
showFullExits = True # whether to show full or brief versions of the current room's exits
hourOfTheDay = 14
hoursSinceLastSlept = 0
health = { 
	'health': 10,
	'maxHealth': 10 
}

class colors:
	HEADER = '\033[95m'
	OKBLUE = '\033[94m'
	OKGREEN = '\033[92m'
	WARNING = '\033[93m'
	FAIL = '\033[91m'

	BOLD = '\033[1m'
	UNDERLINE = '\033[4m'
	FLASHING = '\033[5m'
	INVERSE = '\033[7m'
	HIDDEN = '\033[8m'

	ENDC = '\033[0m'


# All available rooms in this area ######################################################
# Rooms must have DESC and GROUND
townRooms = {
#	'Room Name': {
#		DESC: 'Description of a room',
#		NORTH: 'Name of the room to the north',
#		GROUND: ['Name of an item on the ground', 'Another item name']
#	},
    'The Blacksmith': {
        DESC: 'The room is dim and sooty. A roaring furnace in the back corner eluminates the scaps of matal that litter the ground.',
        GROUND: ['long sword'],
	PEOPLE: ['The Blacksmith'],
        NORTH: 'The Town Square',
        EAST: 'South Y Street'
    },
    'The Deep Forest Wilds': {
        DESC: 'The trees here seem older and larger. Night and day have no meaning. North and South are the same. It\'s impossible to know if the sounds of nature are within or around. The feeling of connectedness, akin to the feeling of alonesness, is just enough.',
	GROUND: [],
        NORTH: 'The Deep Forest Wilds',
        SOUTH: 'The Deep Forest Wilds',
        WEST: 'The Deep Forest Wilds',
        EAST: 'The Deep Forest Wilds'
    },
    'East X Street': {
        DESC: 'This is a decent part of town, but the shadow of The Wizard\'s Tower is everpresent.',
	GROUND: [],
        NORTH: 'The Tavern',
        SOUTH: 'The Wizard\'s Tower',
        WEST: 'The Town Square'
    },
    'The Forest Wilds': {
        DESC: '',
	GROUND: [],
        NORTH: 'The Deep Forest Wilds',
        SOUTH: 'North Y Street',
        WEST: 'The Deep Forest Wilds',
        EAST: 'The Deep Forest Wilds'
    },
    'North Y Street': {
        DESC: 'The north end of Y Street has really gone down hill. A family of rat-like creatures nibble on some scraps thrown out of the tavern.',
        GROUND: ['Do Not Take Sign Sign'],
	NORTH: 'The Forest Wilds',
        SOUTH: 'The Town Square',
        WEST: 'The Thief Guild'
    },
    'South Y Street': {
        DESC: '',
	GROUND: [],
        NORTH: 'The Town Square',
        WEST: 'The Blacksmith'
    },
    'The Tavern': {
	DESC: 'The room is dim but it\'s build suggests that it makes at least some income. The delightful smell of meat pies fills the air.',
        GROUND: ['Shopping HOWTO'],
	ROOMHOURS: (12, 24),
	SHOPHOURS: (12, 24),
	PEOPLE: ['The Bartender', 'Sam Rouk'],
        SOUTH: 'East X Street',
        SELL: {
		'Brew': {
			PRICE: 2,
			ITEMSINSTOCK: 999
		}, 'Mead': {
			PRICE: 2,
			ITEMSINSTOCK: 24
		}, 'Meat Pie': {
			PRICE: 3,
			ITEMSINSTOCK: 999
		}, 'Spiced Potatoes': {
			PRICE: 3,
			ITEMSINSTOCK: 6
		}
	},
    },
    'The Thief Guild': {
        DESC: 'This is a Theif Guild.  I really hate this idea, but I can\'t think of a better thing to put here.',
	GROUND: [],
        SOUTH: 'West X Street',
        EAST: 'North Y Street'
    },
    'The Town Square': {
        DESC: 'The town square is a large open space with a fountain in the center. Streets lead in all directions.',
        GROUND: ['Welcome Sign', 'Fountain'],
        NORTH: 'North Y Street',
        SOUTH: 'South Y Street',
        WEST: 'West X Street',
        EAST: 'East X Street'
    },
    'The Upper Wizard\'s Tower': {
        DESC: '',
	GROUND: [],
        NORTH: 'The Wizard\'s Tower Deck',
        DOWN: 'The Wizard\'s Tower Staircase'
    },
    'The Wizard\'s Tower': {
        DESC: '',
	GROUND: [],
        NORTH: 'East X Street',
        UP: 'The Wizard\'s Tower Staircase'
    },
    'The Wizard\'s Tower Deck': {
        DESC: 'From this deck you can see the whole town. It seems so small from up here.  You feel so small from up here.',
	GROUND: [],
        SOUTH: 'The Wizard\'s Tower Deck',
        UP: 'The Wizard\'s Tower Deck'
    },
    'The Wizard\'s Tower Staircase': {
        DESC: 'The shoulder-width staircase has thin film and damp smell. Except for the occasional archery slit, the darkness is loud and thick.',
	GROUND: [],
        UP: 'The Upper Wizard\'s Tower',
        DOWN: 'The Wizard\'s Tower'
    },
    'West X Street': {
        DESC: 'West X Street Description.',
	GROUND: [],
        NORTH: 'The Thief Guild',
        SOUTH: 'The Blacksmith',
        WEST: 'Ye Old Inn',
        EAST: 'The Town Square'
    },
    'Ye Old Inn North Room': {
        DESC: 'A quaint room. The light from the arched window illuminates what it does have, hand-craftmanship of finest woods.',
        GROUND: ['Bed', 'trunk'],
        SOUTH: 'Ye Old Inn Second Floor',
	ITEMSNEEDEDTOENTER: ['inn key']
    },
    'Ye Old Inn Second Floor': {
        DESC: 'The stairs let out in to a hallway with a window on the other side.  Old family photos line the walls.',
	GROUND: [],
	NORTH: 'Ye Old Inn North Room',
	SOUTH: 'Ye Old Inn South Room',
        DOWN: 'Ye Old Inn'
    },
    'Ye Old Inn South Room': {
        DESC: 'A room erily similar to the one across the hall',
	GROUND: [],
        NORTH: 'Ye Old Inn Second Floor',
	ROOMISENTERABLE: False
    },
    'Ye Old Inn': {
        DESC: 'The first floor is a cheerful, wide open room. The kitchen\'s in the back, and there\'s a warming fire by the staircase.',
	GROUND: [],
        SELL: {
		'inn key': {
			PRICE: 8,
			ITEMSINSTOCK: 1
		}
	},
	SHOPHOURS: (6, 20),
	PEOPLE: ['The Inn Keeper', 'The Knitting Woman'],
        EAST: 'West X Street',
        UP: 'Ye Old Inn Second Floor'
    }
}


# all items available in this area ######################################################
# items must have GROUNDDESC, SHORTDESC, LONGDESC, TAKEABLE, DESCWORDS
townItems = {
#	'Item Name': {
#		GROUNDDESC: 'How this item in descried when on the ground.',
#		SHORTDESC: 'A short description of this item.',
#		LONGDESC: 'A long description of this item, used when the player looks at it.',
#		TAKEABLE: True  # whether this item can be taken and put in your inventory
#		DESCWORDS: ['a word the player can use to refer to this item', 'another item']
#	}
	'Bed': {
		GROUNDDESC: 'A large bed is the prominent feature in the room.',
		SHORTDESC: 'a large bed',
		LONGDESC: 'The bed is large enough for two and has been thoroughly used...',
		TAKEABLE: False,
		SLEEPABLE: 1,
		DESCWORDS: ['bed']
	},
	'Brew': {
		GROUNDDESC: 'A dark brew in an ever-frosty mug.',
		SHORTDESC: 'a dark brew',
		LONGDESC: 'The brew is dark and thick. It smells of coffee and chocolate and the bog deep in the forest wilds.',
		TAKEABLE: True,
		DRINKABLE: 0,
		DESCWORDS: ['brew']
	},
	'Do Not Take Sign Sign': {
		GROUNDDESC: 'A sign stands here not bolted to the ground.',
		SHORTDESC: 'a sign',
		LONGDESC: 'The sign reads, "Do Not Take This Sign"',
		TAKEABLE: True,
		DESCWORDS: ['do not take sign sign', 'sign']
	},
	'donut': {
		GROUNDDESC: 'A donut that looks old but smells amazing.',
		SHORTDESC: 'a donut',
		LONGDESC: 'A bagle shaped donut.',
		TAKEABLE: True,
		EDIBLE: 1,
		DESCWORDS: ['donut', 'breakfast']
	},
	'long sword': {
		GROUNDDESC: 'An ordinary long sword with an ordinary handle.',
		SHORTDESC: 'a sword',
		LONGDESC: 'A longsword is ordinary except for a small engraving.  It reads "bestowed upon the wanderer of The Deep Forest Wilds"',
		TAKEABLE: False, 
		DESCWORDS: ['long sword', 'ordinary long sword', 'sword']
	},
	'Fountain': {
		GROUNDDESC: 'A bubling fountain of green water.',
		SHORTDESC: 'a fountain',
		LONGDESC: 'The water in the fountain is a bright green color. Is that... Mountain Dew',
		TAKEABLE: False,
		DRINKABLE: 0,
		DESCWORDS: ['fountain']
	},
	'inn key': {
		GROUNDDESC: 'a heavy iron key covered in rust',
		SHORTDESC: 'an inn key',
		LONGDESC: 'The key is old but refined as if it guards something important',
		TAKEABLE: True,
		DESCWORDS: ['inn key', 'key']
	},
	'map': {
		GROUNDDESC: 'Something like a schematic is on the ground.',
		SHORTDESC: 'a map',
		LONGDESC: colors.BOLD + """
                     Forest Wilds               
                                                
	     +--------+      +---------+        
	     | Theif  0      | Tavern  |        
	     |  Guild |      |         |        
     +------++------0-+      +----0----+        
     |Ye Old|                                   
     | Inn  |        Town Square    +-------+   
     |      0                       | Deck  |   
     +------+------0----+   +---0-----/  /      
            |  Black-   |   | Wizard /  /       
     	    |    smith  0   | Tower    /        
            +-----------+   +---------+            """ + colors.ENDC,
		TAKEABLE: True,
		DROPABLE: False,
		DESCWORDS: ['map', 'schematic']
	},
	'Mead': {
		GROUNDDESC: 'An aged bottle of mead.',
		SHORTDESC: 'a bottle of mead',
		LONGDESC: 'The fermented honey looks strong and best downed fast.',
		TAKEABLE: True,
		DRINKABLE: -1,
		DESCWORDS: ['mead']
	},
	'Meat Pie': {
		GROUNDDESC: 'A meat pie almost daring you to eat it.',
		SHORTDESC: 'a meat pie',
		LONGDESC: "The meat pie looks even more delicious than it smells. The meat, though indistinguishable, is almost bursting out of the golden crust.",
		TAKEABLE: True,
		EDIBLE: 3,
		DESCWORDS: ['meat pie', 'pie']
	},
	'purse': {
		GROUNDDESC: 'You don\'t have your purse on you! You better take it before someone else does.',
		SHORTDESC: 'a money purse',
		LONGDESC: 'It\'s a leather pouch well oiled and attached to your belt.  It\'s small, but it always seems to be big enough.',
		TAKEABLE: True,
		DROPABLE: False,
		DESCWORDS: ['purse', 'money']	
	},
	'README': {
		GROUNDDESC: 'An old note rests upside down on the ground.',
		SHORTDESC: "README Note",
		LONGDESC: """The README note reads, """ + colors.BOLD + """"Welcome to this text adventure. 

"Note that everything you do takes time. Actions like moving around town may quickly pass you from morn to eve while actions like looking at a sign may pass the time much slower.

“Also note that you have health.  You loose sleep for every day just for living. You can regain health by sleeping, eating, and drinking.

“You can buy things in different places around town. Whereas typing ”look” will show you the items around, type “list” to see if the place your in has anything to buy.

“Items and people in this town can be addressed by multiple names. For instance, you can read this note by typing both ‘look readme’ and ‘look note.'

“Actions and items can be autocompleted by pressing tab. If you try to complete something like “take sig”, and it doesn’t complete, chances are you can’t do that action with that item.

“The map is small, but there is plenty to explore. When in doubt, type ‘help'." """ + colors.ENDC,
		TAKEABLE: True,
		EDIBLE: 0,
		DESCWORDS: ['readme', 'note']
	},
	'Shopping HOWTO': {
		GROUNDDESC: 'A neatly folded guide on how to shop.',
		SHORTDESC: 'a shopping guide',
		LONGDESC: 'Shopping is tough for even the rich.  Here are a few pointers:\n',
		TAKEABLE: True,
		EDIBLE: 0,
		DESCWORDS: ['shopping howto', 'howto', 'shopping guide', 'guide']
	},
	'Spiced Potatoes': {
		GROUNDDESC: 'a basket of spiced potatoes',
		SHORTDESC: 'spiced potatoes',
		LONGDESC: 'A town favorite, spiced potatoes are uncooked, diced tomatoes tossed in wild seasonings. An aquired tasted, but you gotta respect tradition.',
		TAKEABLE: True,
		EDIBLE: 2,
		DESCWORDS: ['spiced potatoes', 'potatoes']
	},
	'trunk': {
		GROUNDDESC: 'A heafty trunk marks the foot of the bed.',
		SHORTDESC: 'a trunk',
		LONGDESC: 'It\'s a trunk similar to those in most homes. There isn\'t anything inside.',
		TAKEABLE: False,
		DESCWORDS: ['trunk', 'heafty trunk']
	},
	'Welcome Sign': {
        	GROUNDDESC: 'A welcome sign stands here.',
        	SHORTDESC: 'a welcome sign',
        	LONGDESC: 'The welcome sign reads,' + colors.BOLD + '"Welcome to town! Adventurers and travelers welcome. Type "help" to see all the things our town has to offer.' + colors.ENDC,
        	TAKEABLE: False,
        	DESCWORDS: ['welcome sign', 'sign']
        }
}


# All people available in this area #####################################################
# people must have GROUNDDESC, LONGDESC, and DESCWORDS
townPeople = {
#	'Person Name': {
#		GROUNDDESC: '',
#		LONGDESC: 'This is what is shown if he is looked at',
#		DIALOG: ["These are things", "he says", "in order"],
#		DESCWORDS: ['These are words', 'used to describe this', 'person']
#		},
	'The Bartender': {
		GROUNDDESC: 'The bartender flashes a grin as he slides a box marked "Not Human Organs" under a table with his foot.',
		LONGDESC: 'The bartender has a kept appearance, but there\'s something more going on behind his eyes.',
		DIALOG: [colors.BOLD + "Welcome! It\'s good to see a nice, spry traveller this time of year.", 
			 "Try the mead if you\'re unsure of what to get. It\'s the inn keeper\' favorite.",
			 "He\'s a lonesome sort you know. Why don\'t you give him a shout for me?", 
			 "Anyway, What'll you have?",
			 "If you have any questions, please look at our shopping guide." + colors.ENDC], 
		DESCWORDS: ['bartender']
	},
	'The Blacksmith': {
		GROUNDDESC: 'A man has his back towards you. He\'s furiously hammering.',
		LONGDESC: 'The blacksmith is a sturdy man with a broad smaile and an even broader forearms',
		DIALOG: [colors.BOLD + "Welcome traveler.  I don't think I've seen you around here before.",
			 "What?! You don't seem armed. That's not wise around these parts...",
			 "the forest wilds have been more and more active since that wizard came to town.", "Here. I like you. Feel free to take one of my swords." + colors.ENDC], 
		MAKEITEMSTAKEABLEAFTERDIALOG: ['long sword'],
		DESCWORDS: ['blacksmith', 'man']
	},
	'The Knitting Woman': {
		GROUNDDESC: 'An old woman knits as she rocks in front of the fire.',
		LONGDESC: 'The woman look similar to the inn keeper. She seems as much a part of the room as the fireplace the the large oak table.',
		DESCWORDS: ['knitting woman', 'woman']
	},
	'The Inn Keeper': {
		GROUNDDESC: 'The inn keeper makes his way over from cleaning the eating area as you walk in.',
		LONGDESC: 'The old man has has a frail frame and kindness in his eyes.',
		DIALOG: [colors.BOLD + "What's the word traveler? Dangerous times these days.", 
			 "We've been here since the start of this town, and it looks like we might be here at its end.", 
			 "We have rooms available. Let me know if you're interested." + colors.ENDC], 
		DESCWORDS: ['inn keeper', 'keeper', 'man']
	},
	'Sam Rouk': {
		GROUNDDESC: 'A man sits alone in the corner nursing a pint. It looks like he\'s staring straight at you.',
		LONGDESC: 'After a closer look, the man\'s gaze is a thousand miles and a thousand years from here.',
		DIALOG: [colors.BOLD + "\"Hm...  Name's... ... Sam...\"" + colors.ENDC + " he says", 
			 "He doesn't shift his gaze from the place you came in.",
			 "He mumbles something else inaudible before returning to his brew.",],
		DESCWORDS: ['man', 'sam', 'Sam']
	}
}
	

# Display Functions ####################################################################
def displayLocation(area):
	global hourOfTheDay

	print("\n")
	print(area)
	print("=" * len(area))
	print(" Health: [", end="")
	print("/" * getHealth(), end="")
	print(" " * (getMaxHealth() - getHealth()), end="")
	print("]")
	print(" Shallots: " + str(getPurse()) + " | " + getDisplayTime())
	print("")
	print(townRooms[area][DESC], end="")
	
	if PEOPLE in townRooms[area]:
		for person in townRooms[area][PEOPLE]:
			print(" " + townPeople[person][GROUNDDESC], end="")
	print("")

	if len(townRooms[area][GROUND]) > 0:
		print("")
		for groundObject in townRooms[area][GROUND]:	
			print(townItems[groundObject][GROUNDDESC])

	exits = []
	for direction in (NORTH, SOUTH, WEST, EAST, UP, DOWN):
		if direction in townRooms[area].keys(): 
			exits.append(direction.title())
	print("")
	if showFullExits:
		for direction in (NORTH, SOUTH, WEST, EAST, UP, DOWN):
			if direction in townRooms[area]:
				print("{}: {}".format(direction, townRooms[area][direction]))
	else:
		print('Exits:', end=" ")
		for exit in exits:
			print(exit, end=' ')
	print("")

def moveDirection(direction):
	global location

	if direction not in townRooms[location]:
		print('You cannot move in that direction')
		return

	if ROOMISENTERABLE in townRooms[townRooms[location][direction]].keys():
		if townRooms[townRooms[location][direction]][ROOMISENTERABLE] == False:
			print("You can't go into", townRooms[location][direction] + ".")
			return

	if ITEMSNEEDEDTOENTER in townRooms[townRooms[location][direction]].keys():
		for item in townRooms[townRooms[location][direction]][ITEMSNEEDEDTOENTER]:
			if item not in inventory:
				print("You can't go into", townRooms[location][direction] + ". You don't have", townItems[item][SHORTDESC])
				return
			else:
				print(townItems[item][SHORTDESC], "got you into", townRooms[location][direction] + ".", end=" ")

	if ROOMHOURS in townRooms[townRooms[location][direction]].keys():
		(openHour, closeHour) = townRooms[townRooms[location][direction]][SHOPHOURS]
		if getTime() < openHour or getTime() > closeHour:
			closedString = townRooms[location][direction] + " isn't open right now"
			if getDisplayTime() == getDisplayTime(openHour):
				extra = ", but stick around because it's about to be."
			else:
				extra = ". Try again around " + getDisplayTime(openHour) + "."

			print(closedString + extra)
			return

	print('You move to the %s.' % direction)
	location = townRooms[location][direction]
	displayLocation(location)

def animateBoldTextPlaces(mapString):
# These places are hard coded here.  
# If you changed a places name in townPlaces, you must also change it here.
# If you add a place to the map, you must also add the check here.

	if location == 'The Forest Wilds' or location == 'The Deep Forest Wilds':
		mapString = mapString.replace("Forest Wilds", colors.FLASHING + "Forest Wilds" + colors.ENDC + colors.BOLD)
	elif location == 'The Thief Guild':
		mapString = mapString.replace("Theif", colors.FLASHING + "Theif" + colors.ENDC + colors.BOLD)
		mapString = mapString.replace("Guild", colors.FLASHING + "Guild" + colors.ENDC + colors.BOLD)
	elif location == 'The Tavern':
		mapString = mapString.replace("Tavern", colors.FLASHING + "Tavern" + colors.ENDC + colors.BOLD)
	elif location == "Ye Old Inn" or location == "Ye Old Inn Second Floor" or location == "Ye Old Inn South Room" or location == "Ye Old Inn North Room":
		mapString = mapString.replace("Ye Old", colors.FLASHING + "Ye Old" + colors.ENDC + colors.BOLD)
		mapString = mapString.replace("Inn", colors.FLASHING + "Inn" + colors.ENDC + colors.BOLD)
	elif location == 'The Town Square':
		mapString = mapString.replace("Town Square", colors.FLASHING + "Town Square" + colors.ENDC + colors.BOLD)
	elif location == 'The Blacksmith':
		mapString = mapString.replace("Blacksmith", colors.FLASHING + "Blacksmith" + colors.ENDC + colors.BOLD)
	elif location == 'The Wizard\'s Tower' or location == 'The Wizard\'s Tower Staircase' or location == 'The Upper Wizard\'s Tower':
		mapString = mapString.replace("Wizard", colors.FLASHING + "Wizard" + colors.ENDC + colors.BOLD)
		mapString = mapString.replace("Tower", colors.FLASHING + "Tower" + colors.ENDC + colors.BOLD)
	elif location == 'The Wizard\'s Tower Deck':
		mapString = mapString.replace("Deck", colors.FLASHING + "Deck" + colors.ENDC + colors.BOLD)
		
	return colors.BOLD + mapString + colors.ENDC
		

# Getters and Setters ##################################################################
def updateHealth(healthOffset):
	health['health'] += healthOffset

	if getHealth() > getMaxHealth():
		health['health'] = getMaxHealth()		

def getHealth():
	return health['health']

def getMaxHealth():
	return health['maxHealth']

def updateTime(hours):
	global hourOfTheDay
	global hoursSinceLastSlept
	hourOfTheDay = (hourOfTheDay + hours) % 24
	hoursSinceLastSlept += hours
		
	if hoursSinceLastSlept > 24:
		updateHealth(-1)
		hoursSinceLastSlept = hoursSinceLastSlept % 24
			
	return

def getTime():
	global hourOfTheDay
	return hourOfTheDay	

def getDisplayTime(hour = -1):
	global hourOfTheDay
	displayString = ""
	
	if hour == -1:
		hour = hourOfTheDay
	
	if hour <= 4 or hour > 22:
		displayString = "Nigh"
	elif hour <= 10 and hour > 4:
		displayString = "Morn"
	elif hour <= 16 and hour > 10:
		displayString = "Midday"
	elif hour <= 22 and hour > 16:
		displayString = "Eve"
	else:
		displayString = hour
	return displayString

def getPurse():
	global purse
	return purse

def updatePurse(shallots):
	global purse
	newPurse = purse + shallots
	if newPurse < 0:
		return False
	else:
		purse = newPurse
		return True


# Utility Classes ######################################################################
def getAllDescWords(itemList):
	uniqueItemList = list(set(itemList))
	totalDescWords = []	
	for item in uniqueItemList:
		totalDescWords.extend(townItems[item][DESCWORDS])
	return list(set(totalDescWords))

def getAllFirstDescWords(itemList):
	uniqueItemList = list(set(itemList))
	firstDescWords = []	
	for item in uniqueItemList:
		firstDescWords.append(townItems[item][DESCWORDS][0])
	return list(set(firstDescWords))
		
def getFirstItemMatchingDesc(desc, itemList):
	uniqueItemList = list(set(itemList))
	for item in uniqueItemList:
		if desc in townItems[item][DESCWORDS]:
			return item
	return None

def getAllItemsMatchingDesc(desc, itemList):
	uniqueItemList = list(set(itemList))
	matchingItems = []
	for item in uniqueItemList:
		if desc in townItems[item][DESCWORDS]:
			matchingItems.append(item)
	return matchingItems

def getAllPeopleDescWords(personList):
	uniquePersonList = list(set(personList))
	totalDescWords = []	
	for person in uniquePersonList:
		totalDescWords.extend(townPeople[person][DESCWORDS])
	return list(set(totalDescWords))

def getAllFirstPersonDescWords(personList):
	matchingPeople = list(set(personList))
	firstDescWords = []	
	for person in matchingPeople:
		firstDescWords.append(townPeople[person][DESCWORDS][0])
	return list(set(firstDescWords))

def getFirstPersonMatchingDesc(desc, personList):
	uniquePersonList = list(set(personList))
	for person in uniquePersonList:
		if desc in townPeople[person][DESCWORDS]:
			return person
	return None

def getAllPeoleMatchingDesc(desc, personList):
	uniquePersonList = list(set(personList))
	matchingPeople = []
	for person in uniquePersonList:
		if desc in townPeople[person][DESCWORDS]:
			matchingPeople.append(person)
	return matchingPeople


# Main Adventure Command Loop ###########################################################
class TextAdventureCmd(cmd.Cmd): 
	prompt = '\n> '

	# When other do_*() command methods don't match 
	def default(self, arg):
		print('I do not understand that command. Type "help" for a list of commands.')

	# This exits the Cmd application loop
	def do_quit(self, arg):
		"""Quit the game."""
		return True

	def do_clear(self, arg):
		"""Start with a the command line at the top of a fresh page."""
		os.system('clear')

	def postcmd(self, stop, line):
		lineList = line.split(" ")
		lastCommand = lineList[0]
		newStop = False
		if 'help' == lastCommand:
			pass
		elif lastCommand in ('north', 'west', 'east', 'south', 'up', 'down', 'n', 'w', 'e', 's', 'u', 'd'):
			updateTime(3)
		elif lastCommand == 'look':
			updateTime(1)
		elif lastCommand == 'sleep':
			if len(lineList) > 1:
				# there's an item designated to sleep on
				updateTime(8)
			else:
				updateTime(2)
				
		else:
			updateTime(2)

		if getHealth() <= 0:
			print("\n" + colors.FAIL + ' *** You are drained of strength.  You eyes flicker shut. You fight to reopen them, but the dazling colors and soft shapes carry you down. ***\n' + colors.ENDC)
			stop = True
		return stop 
	
	# Movement Commands
	def do_north(self, arg):
		"""Go to the area to the north if possible"""
		moveDirection('north')

	def do_south(self, arg):
		"""Go to the area to the south if possible"""
		moveDirection('south')

	def do_west(self, arg):
		"""Go to the area to the west if possible"""
		moveDirection('west')

	def do_east(self, arg):
		"""Go to the area to the east if possible"""
		moveDirection('east')

	def do_up(self, arg):
		"""Go to the area upwards if possible"""
		moveDirection('up')

	def do_down(self, arg):
		"""Go to the area to the down if possible"""
		moveDirection('down')

	def do_exits(self, arg):
		"""Toggle showing full exit descriptions or brief exit descriptions.""" 
		global showFullExits
		showFullExits = not showFullExits
		if showFullExits:
			print('Showing full exit descriptions.')
		else:
			print('Showing brief exit descriptions.')
		
	def do_inventory(self, arg):
		"""Display a list of the items in your possession."""

		if len(inventory) == 0:
			print('Inventory:\n (nothing)')
			return

		# first get a count of each distinct item in the inventory
		itemCount = {}
		for item in inventory:
			if item in itemCount.keys():
				itemCount[item] += 1
			else:
				itemCount[item] = 1

		# get a list of inventory items with duplicates removed
		print('Inventory:')
		for item in itemCount:
			itemNumerString = ""
			if itemCount[item] > 1:
				itemNumerString = "(" + str(itemCount[item]) + ")"		
			print(' {} {}'.format(item, itemNumerString))

	def do_take(self, arg):
		""""take <item> - Take an item on the ground."""
    
		# put this value in a more suitably named variable
		itemToTake = arg.lower()

		if not itemToTake:
			print('Take what? Type "look" the items on the ground here.')
			return

		cantTake = False

		# get the item name that the player's command describes
		for item in getAllItemsMatchingDesc(itemToTake, townRooms[location][GROUND]):
			if townItems[item].get(TAKEABLE, True) == False:
				cantTake = True
				continue 

			print('You take %s.' % (townItems[item][SHORTDESC]))
			townRooms[location][GROUND].remove(item) # remove from the ground
			inventory.append(item) # add to inventory
			return

		if cantTake:
			print('You cannot take "%s".' % (itemToTake))
		else:
			print('That is not on the ground.')

	def complete_take(self, text, line, egidx, endinx):
		possibleItems = []
		text = text.lower()

		groundDescWords = getAllDescWords(townRooms[location][GROUND])
		for descWord in groundDescWords:
			if line.startswith('take %s' % (descWord)):
				return []

		# if the user hasn't typed an item name:
		if not text:
			return getAllFirstDescWords(townRooms[location][GROUND])

		# otherwise, get a list of all "description words" for ground items
		for descWord in groundDescWords:
			if descWord.startswith(text):
				possibleItems.append(descWord)
		return list(set(possibleItems))

	def do_drop(self, arg):
		""""drop <item> - Drop an item from your inventory onto the ground."""

		itemToDrop = arg.lower()
		invDescWords = getAllDescWords(inventory)

		if itemToDrop not in invDescWords:
			print('You do not have "%s" in your inventory.' % (itemToDrop))
			return

		item = getFirstItemMatchingDesc(itemToDrop, inventory)
		if item != None:
			if DROPABLE in townItems[item].keys():
				if townItems[item][DROPABLE] == False:
					print('Nice try, but you can\'t drop that.')
					return
			print('You drop %s.' % (townItems[item][SHORTDESC]))
			inventory.remove(item)
			townRooms[location][GROUND].append(item)

	def complete_drop(self, text, line, egidx, endinx):
		possibleItems = []
		itemToDrop = text.lower()

		invDescWords = getAllDescWords(inventory)
		for descWord in invDescWords:
			if line.startswith('drop %s' % (descWord)):
				return []

		# if the user hasn't typed an item name:
		if not itemToDrop:
			return getAllFirstDescWords(inventory)

		# otherwise, get a list of all "description words" for inventory items
		for descWord in invDescWords:
			if descWord.startswith(text):
				possibleItems.append(descWord)
		return list(set(possibleItems))

	def do_look(self, arg):
		"""Look at an item, direction, or the area:
"look" - display the current area's description
"look <direction>" - display the description of the area in that direction
"look exits" - display the description of all adjacent areas
"look <attribute>" - display 'time', 'place', 'money, or'health'
"look <person>" - display the description of another person 
"look <item>" - display the description of an item on the ground or in your inventory"""

		lookingAt = arg.lower()
		if lookingAt == '':
		# "look" prints the area description
			displayLocation(location)
			return

		if lookingAt == 'exits':
			for direction in (NORTH, SOUTH, WEST, EAST, UP, DOWN):
				if direction in townRooms[location]:
					print('%s: %s' % (direction.title(), townRooms[location][direction]))
			return

		if lookingAt in ('north', 'west', 'east', 'south', 'up', 'down', 'n', 'w', 'e', 's', 'u', 'd'):
			if lookingAt.startswith('n') and NORTH in townRooms[location]:
				print(townRooms[location][NORTH])
			elif lookingAt.startswith('s') and SOUTH in townRooms[location]:
				print(townRooms[location][SOUTH])
			elif lookingAt.startswith('w') and WEST in townRooms[location]:
				print(townRooms[location][WEST])
			elif lookingAt.startswith('e') and EAST in townRooms[location]:
				print(townRooms[location][EAST])
			elif lookingAt.startswith('u') and UP in townRooms[location]:
				print(townRooms[location][UP])
			elif lookingAt.startswith('d') and DOWN in townRooms[location]:
				print(townRooms[location][DOWN])
			else:
				print('There is nothing in that direction.')
			return

		if lookingAt == 'admin':
			global hoursSinceLastSlept
			print('Health', health['health'])
			print('Location:', location)
			print('Shallots:', getPurse())
			print('Time:', hourOfTheDay)
			print('HoursSinceLastSlept', hoursSinceLastSlept)
			return

		if lookingAt == 'time':
			print('Time:', getDisplayTime())
			return

		if lookingAt == 'place':
			print('Place:', location)
			return

		if lookingAt == 'money' or lookingAt == 'shallots':
			print('Shallots: ' + str(getPurse()))
			return

		if lookingAt == 'health':
			print('Health: ' + str(int((getHealth()/getMaxHealth())*100)) + "%")
			return

		if PEOPLE in townRooms[location]:
			person = getFirstPersonMatchingDesc(lookingAt, townRooms[location][PEOPLE])
			if person != None:
				print(townPeople[person][LONGDESC])
				return

		subject = None
		groundItem = getFirstItemMatchingDesc(lookingAt, townRooms[location][GROUND])
		if groundItem != None:
			subject = groundItem
		inventoryItem = getFirstItemMatchingDesc(lookingAt, inventory)
		if inventoryItem != None:
			subject = inventoryItem

		if subject != None:
			description = townItems[subject][LONGDESC]
			if subject == 'map':
				description = animateBoldTextPlaces(description)
			print(description)
			return
		
		print('You do not see that nearby.')

	def complete_look(self, text, line, begidx, endidx):
		possibleItems, allDescWords = [], []
		lookingAt = text.lower()

		# get a list of all "description words" for each item
		allDescWords.extend(getAllDescWords(inventory))
		allDescWords.extend(['exits','place','time','money','health'])
		allDescWords.extend([NORTH,SOUTH,EAST,WEST,UP,DOWN])
		allDescWords.extend(getAllDescWords(townRooms[location][GROUND]))
		if SELL in townRooms[location].keys():
			allDescWords.extend(getAllDescWords(townRooms[location][SELL]))
		if PEOPLE in townRooms[location].keys():
			allDescWords.extend(getAllPeopleDescWords(townRooms[location][PEOPLE]))

		# return nothing if full item is typed
		for descWord in allDescWords:
			if line.startswith('look %s' % (descWord)):
				return []

		# return every first description if nothing is typed
		if lookingAt == '':
			possibleItems.extend(getAllFirstDescWords(inventory))
			possibleItems.extend(['exits','place','time','money','health'])
			for direction in (NORTH, SOUTH, EAST, WEST, UP, DOWN):
				if direction in townRooms[location]:
					possibleItems.append(direction)
			possibleItems.extend(getAllFirstDescWords(townRooms[location][GROUND]))
			if SELL in townRooms[location].keys():
				possibleItems.extend(getAllFirstDescWords(townRooms[location][SELL]))
			if PEOPLE in townRooms[location].keys():	
				possibleItems.extend(getAllFirstPersonDescWords(townRooms[location][PEOPLE]))		
			return list(set(possibleItems))

		# if partial is typed, return matches
		for descWord in allDescWords:
			if descWord.startswith(lookingAt):
				possibleItems.append(descWord)

		return list(set(possibleItems))

	def do_list(self, arg):
		"""List the items for sale at the current location's shop. "list full" will show details of the items."""
        
		if SELL not in townRooms[location]:
			print('This isn\'t a shop my man!')
			return

		arg = arg.lower()
		print("Your purse: (S%s)\n" % (getPurse()))
	
		print("For Sale:")
		for item in townRooms[location][SELL]:
			if townRooms[location][SELL][item][ITEMSINSTOCK] > 0:
				itemDesc = 'S' + str(townRooms[location][SELL][item][PRICE])
			else:
				itemDesc = 'Out of Stock'
			print('  - %s%s (%s)%s' % (colors.UNDERLINE, item, itemDesc, colors.ENDC), end="")
			if arg == 'full':
				print(":", townItems[item][LONGDESC], end="")
			print("")

	def complete_list(self, text, line, begidx, endidx):
		attributeOfList = text.lower()

		if not attributeOfList:
			return ['full']
		
		if attributeOfList.startswith(attributeOfList):
			return ['full']

	def do_buy(self, arg):
		""""buy <item>" - buy an item at the current location's shop."""
		if SELL not in townRooms[location]:
			print('This isn\'t a shop my man.')
			return

		if SHOPHOURS in townRooms[location].keys():
			(openHour, closeHour) = townRooms[location][SHOPHOURS]
			if getTime() < openHour or getTime() > closeHour:
				closedString = "This shop is closed"
				if getDisplayTime() == getDisplayTime(openHour):
					extra = ", but stick around because we're about to open my man."
				else:
					extra = ". Please come back around " + getDisplayTime(openHour) + " my man."

				print(closedString + extra)
				return

		itemToBuy = arg.lower()	
		if itemToBuy == '':
			print('Buy what? Type "list" or "list full" to see a list of items for sale.')
			return
		
		item = getFirstItemMatchingDesc(itemToBuy, townRooms[location][SELL])
		if item != None:
			if townRooms[location][SELL][item][ITEMSINSTOCK] > 0:
				if updatePurse(0 - townRooms[location][SELL][item][PRICE]):
					print('You purchased %s for %s Shallots my man.' % (townItems[item][SHORTDESC], townRooms[location][SELL][item][PRICE]))
					inventory.append(item)
					townRooms[location][SELL][item][ITEMSINSTOCK] -= 1
					return
				else:
					print('Ah. You don\'t have enough shallots to cover that my man.')	
					return
			else:
				print('Ah. My apologies, but we\'re out of stock on that my man.')
				return

		print('"%s" is not sold here my man. Type "list" or "list full" to see a list of items for sale.' % (itemToBuy))

	def complete_buy(self, text, line, begidx, endidx):
		possibleItems = []
		itemToBuy = text.lower()

		sellDescWords = getAllDescWords(townRooms[location][SELL])
		for descWord in sellDescWords:
			if line.startswith('buy %s' % (descWord)):
				return []

		# if the user hasn't typed an item name:
		if not itemToBuy:
			return getAllFirstDescWords(townRooms[location][SELL])

		# otherwise, get a list of all "description words" for ground items
		for descWord in sellDescWords:
			if descWord.startswith(itemToBuy):
				possibleItems.append(descWord)
		return list(set(possibleItems))

	def do_sell(self, arg):
		""""sell <item>" - sell an item at the current location's shop."""
		if SELL not in townRooms[location]:
			print('This is not a shop.')
			return

		if SHOPHOURS in townRooms[location].keys():
			(openHour, closeHour) = townRooms[location][SHOPHOURS]
			if getTime() < openHour or getTime() > closeHour:
				closedString = "This shop is closed"
				if getDisplayTime() == getDisplayTime(openHour):
					extra = ", but stick around because we're about to open my man."
				else:
					extra = ". Please come back around " + getDisplayTime(openHour) + " my man."

				print(closedString + extra)
				return

		itemToSell = arg.lower()
		if itemToSell == '':
			print('Sell what? Type "inventory" or "inv" to see your inventory.')
			return

		for item in inventory:
			if itemToSell in townItems[item][DESCWORDS]:
				if item in townRooms[location][SELL].keys():
					buyPrice = int(townRooms[location][SELL][item][PRICE] * 0.7)
					print('You have sold %s for %s Shallots my man.' % (townItems[item][SHORTDESC], buyPrice))
					inventory.remove(item)
					townRooms[location][SELL][item][ITEMSINSTOCK] += 1
					updatePurse(buyPrice)
					return
				else:
					print('Ah. We don\'t deal in that type of item my man.')
					return

		print('You do not have "%s". Type "inventory" or "inv" to see your inventory.' % (itemToSell))

	def complete_sell(self, text, line, egidx, endinx):
		possibleItems = []
		itemToSell = text.lower()

		invDescWords = getAllDescWords(inventory)
		for descWord in invDescWords:
			if line.startswith('sell %s' % (descWord)):
				return []

		# if the user hasn't typed an item name:
		if not itemToSell:
			return getAllFirstDescWords(inventory)

		# otherwise, get a list of all "description words" for inventory items
		for descWord in invDescWords:
			if descWord.startswith(itemToSell):
				possibleItems.append(descWord)
		return list(set(possibleItems))

	def do_eat(self, arg):
		""""eat <item> - Eat an item. This will only temporarily remove the item from the inventory or the area, but you probably won't want to us it again..."""
		itemToEat = arg
		groundItem = getFirstItemMatchingDesc(itemToEat, townRooms[location][GROUND])
		if groundItem != None:
			if EDIBLE in townItems[groundItem].keys():
				print('You ate %s.' % (townItems[groundItem][SHORTDESC]))
				updateHealth( townItems[groundItem][EDIBLE] )
				townRooms[location][GROUND].remove(groundItem)
				return
			else:
				print('You can\'t eat that!')
				return
            
		invItem = getFirstItemMatchingDesc(itemToEat, inventory)
		if invItem != None:
			if EDIBLE in townItems[invItem].keys():
				print('You ate %s.' % (townItems[invItem][SHORTDESC]))
				updateHealth( townItems[invItem][EDIBLE] )
				inventory.remove(invItem)
				return
			else:
				print('You can\'t eat that!')
				return

		print('Eat what? Type "help eat" for more information.')

	def complete_eat(self, text, line, begidx, endidx): 
		possibleItems, allDescWords = [], []
		itemToEat = text.lower()
		
		allDescWords.extend(getAllDescWords(inventory))
		allDescWords.extend(getAllDescWords(townRooms[location][GROUND]))

		if not itemToEat:
			possibleItems.extend(getAllFirstDescWords(inventory))
			possibleItems.extend(getAllFirstDescWords(townRooms[location][GROUND]))
			return list(set(possibleItems))

		# return nothing if full item is typed
		for descWord in allDescWords:
			if line.startswith('eat %s' % (descWord)):
				return []

		# if partial is typed, return matches
		for descWord in allDescWords:
			if descWord.startswith(itemToEat):
				possibleItems.append(descWord)

		return list(set(possibleItems))

	def do_drink(self, arg):
		""""eat <item> - Drink an item. Down the hatch."""
		itemToDrink = arg
		groundItem = getFirstItemMatchingDesc(itemToDrink, townRooms[location][GROUND])
		if groundItem != None:
			if DRINKABLE in townItems[groundItem].keys():
				print('You drank %s.' % (townItems[groundItem][SHORTDESC]))
				updateHealth( townItems[groundItem][DRINKABLE] )
				townRooms[location][GROUND].remove(groundItem)
				return
			else:
				print('You can\'t drink that!')
				return
            
		invItem = getFirstItemMatchingDesc(itemToDrink, inventory)
		if invItem != None:
			if DRINKABLE in townItems[invItem].keys():
				print('You drank %s.' % (townItems[invItem][SHORTDESC]))
				updateHealth( townItems[invItem][DRINKABLE] )
				inventory.remove(invItem)
				return
			else:
				print('You can\'t drink that!')
				return

		print('Drink what? Type "help drink" for more information.')

	def complete_drink(self, text, line, begidx, endidx): 
		possibleItems, allDescWords = [], []
		itemToDrink = text.lower()
		
		allDescWords.extend(getAllDescWords(inventory))
		allDescWords.extend(getAllDescWords(townRooms[location][GROUND]))

		if not itemToDrink:
			possibleItems.extend(getAllFirstDescWords(inventory))
			possibleItems.extend(getAllFirstDescWords(townRooms[location][GROUND]))
			return list(set(possibleItems))

		# return nothing if full item is typed
		for descWord in allDescWords:
			if line.startswith('drink %s' % (descWord)):
				return []

		# if partial is typed, return matches
		for descWord in allDescWords:
			if descWord.startswith(itemToDrink):
				possibleItems.append(descWord)

		return list(set(possibleItems))

	def do_talk(self, arg):
		""""talk <person> - Talk with another person."""
    
		# put this value in a more suitably named variable
		personToTalkTo = arg.lower()

		if personToTalkTo == '':
			print('Talk to whome? Type "look" to see the people here.')
			return

		if PEOPLE not in townRooms[location]:
			print("Um... There's no one here.")
			return
		
		# get the item name that the player's command describes
		person = getFirstPersonMatchingDesc(personToTalkTo, townRooms[location][PEOPLE])
		if person != None:	
			if DIALOG in townPeople[person].keys():
				for line in townPeople[person][DIALOG]:
					input(line)
				if MAKEITEMSTAKEABLEAFTERDIALOG in townPeople[person].keys():
					for item in townPeople[person][MAKEITEMSTAKEABLEAFTERDIALOG]:
						townItems[item][TAKEABLE] = True
				return
			else:
				print('It looks like', person, 'isn\'t interested in talking.')
				return

		print('There\'s no one here by that name. Try again.')

	def complete_talk(self, text, line, begidx, endidx):
		possibleItems, allDescWords = [], []
		peopleToTalkTo = text.lower()
		
		if PEOPLE in townRooms[location].keys():
			allDescWords.extend(getAllPeopleDescWords(townRooms[location][PEOPLE]))
		
		if not peopleToTalkTo:
			if PEOPLE in townRooms[location].keys():
				possibleItems.extend(getAllFirstPersonDescWords(townRooms[location][PEOPLE]))
			return list(set(possibleItems))

		# return nothing if full item is typed
		for descWord in allDescWords:
			if line.startswith('talk %s' % (descWord)):
				return []

		# if partial is typed, return matches
		for descWord in allDescWords:
			if descWord.startswith(peopleToTalkTo):
				possibleItems.append(descWord)

		return list(set(possibleItems))


	def do_sleep(self, arg):
		""""sleep - sleep right here
sleep <item> - Sleep on an item."""
		itemToSleepOn = arg

		if itemToSleepOn == '':
			print('You awake from the ground. It wasn\'t refreshing, but it\'ll do.')
			return
         
		groundItem = getFirstItemMatchingDesc(itemToSleepOn, townRooms[location][GROUND])
		if groundItem != None:
			if SLEEPABLE in townItems[groundItem].keys():
				print('You awake feeling refreshed.')
				updateHealth(townItems[groundItem][SLEEPABLE])
				return
			else:
				print('You can\'t sleep on that!')
				return

	def complete_sleep(self, text, line, begidx, endidx):
		possibleItems, allDescWords = [], []
		itemToSleepOn = text.lower()
		
		allDescWords.extend(getAllDescWords(townRooms[location][GROUND]))

		if not itemToSleepOn:
			possibleItems.extend(getAllFirstDescWords(townRooms[location][GROUND]))
			return list(set(possibleItems))

		# return nothing if full item is typed
		for descWord in allDescWords:
			if line.startswith('sleep %s' % (descWord)):
				return []

		# if partial is typed, return matches
		for descWord in allDescWords:
			if descWord.startswith(itemToSleepOn):
				possibleItems.append(descWord)

		return list(set(possibleItems))
			

	# Movement shortcuts
	do_n = do_north
	do_s = do_south
	do_w = do_west 
	do_e = do_east 
	do_u = do_up 
	do_d = do_down
	do_inv = do_inventory
	do_q = do_quit
	
	def help_arrowUp(self):
		print('Arrow up to see past commands.')
	def help_combat(self):
		print('Combat is not implemented in this program.')
	def help_tabComplete(self):
		print('Press tab to complete commands and items. If the completion stops midword, hit tab twice to display all available options.')

	
# Start Up #############################################################################
def getTitleString():
	return """
    _____   ___    _    _   _   _        ___     _   _   ____   ____   _____    
   [_   _] / _ \  | |  | | | \ | |      / _ \   | | | | |  __] |  __] [_   _]   
_____| |__| / \ |_| |  | |_|  \| |_____| / \ |__| |_| |_| [__ _| [__ ___| |_____
=====| |==| | | |=| |/\| |=|     |=====| |=| |==| |=| |=|  __]=|__  |===| |=====
_____| |__| \_/ |_|      |_| |\  |_____| \_/  \_| \_/ |_| [__ _ __] |___| |_____
     |_|   \___/  |__/\__| |_| \_|      \___/\|  \___/  |____] [____|   |_|     
									        """

if __name__ == '__main__':
	os.system('clear') 
	print(getTitleString()) 
	print()
	print(colors.WARNING + colors.BOLD + '(Type "help" for commands.)' + colors.ENDC) 
	displayLocation(location) 
	TextAdventureCmd().cmdloop() 
	print('Thanks for playing!\n')

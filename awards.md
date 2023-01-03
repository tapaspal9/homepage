<button id="theme-toggle" onclick="modeSwitcher()"></button>

const theme = localStorage.getItem('theme');
if (theme === "dark") {
document.documentElement.setAttribute('data-theme', 'dark');
}

---
layout: page
title: Awards
---
* I am awarded postdoctoral research fellowship by
  the Strategic Centre for Research in Privacy-Preserving Technologies & Systems ([SCRIPTS](https://www.ntu.edu.sg/scripts)) at [NTU](https://www.ntu.edu.sg) Singapore.

* I am awarded [ERCIM](https://www.ercim.eu) postdoctoral fellowship by 
  the European Research Consortium for Informatics and Mathematics at [NTNU](https://www.ntnu.no) Norway.
  
* I served as a _Senior Research Fellow_ from 2018 to 2021 at the department of [Mathematics](http://www.iitkgp.ac.in/department/MA) of 
  [Indian Institute of Technology Kharagpur](http://www.iitkgp.ac.in).  
  
* I was awarded _Junior Research Fellowship_ in the year 2016 by the department of [Mathematics](http://www.iitkgp.ac.in/department/MA) of 
  [Indian Institute of Technology Kharagpur](http://www.iitkgp.ac.in).
  
* I was awarded _Junior Research Fellowship_ in the year 2016 by the [University Grants Commission](https://www.ugc.ac.in/#) of India.  

* I was awarded _First Class First_ in Mathematics with Honors by the [University of Calcutta](http://www.caluniv.ac.in).   

* I was awarded _Krishnalal De Medal_ for The Best Performance Mathematics in the year 2014 by 
  the [Scottish Church College](https://www.scottishchurch.ac.in) of Kolkata.

* I was awarded _Prof. Alexander Thomson Prize_ for Mathematics in the year 2013 by 
  the [Scottish Church College](https://www.scottishchurch.ac.in) of Kolkata.
  
* I was awarded _Dr. Alexander Duff Memorial Prize_ for Mathematics in the year 2013 by 
  the [Scottish Church College](https://www.scottishchurch.ac.in) of Kolkata. 


const userPrefers = getComputedStyle(document.documentElement).getPropertyValue('content');	

if (theme === "dark") {
	document.getElementById("theme-toggle").innerHTML = "Light Mode";
} else if (theme === "light") {
	document.getElementById("theme-toggle").innerHTML = "Dark Mode";
} else if  (userPrefers === "dark") {
	document.documentElement.setAttribute('data-theme', 'dark');
	window.localStorage.setItem('theme', 'dark');
	document.getElementById("theme-toggle").innerHTML = "Light Mode";
} else {
	document.documentElement.setAttribute('data-theme', 'light');
	window.localStorage.setItem('theme', 'light');
	document.getElementById("theme-toggle").innerHTML = "Dark Mode";
}

function modeSwitcher() {
	let currentMode = document.documentElement.getAttribute('data-theme');
	if (currentMode === "dark") {
		document.documentElement.setAttribute('data-theme', 'light');
		window.localStorage.setItem('theme', 'light');
		document.getElementById("theme-toggle").innerHTML = "Dark Mode";
	} else {
		document.documentElement.setAttribute('data-theme', 'dark');
		window.localStorage.setItem('theme', 'dark');
		document.getElementById("theme-toggle").innerHTML = "Light Mode";
	}
}

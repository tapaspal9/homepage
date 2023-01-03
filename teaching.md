---
layout: page
title: Teaching
---

<button id="theme-toggle" onclick="modeSwitcher()"></button>

const theme = localStorage.getItem('theme');
if (theme === "dark") {
document.documentElement.setAttribute('data-theme', 'dark');
}

### Teaching Assistant

* 2020 Sep-Dec **Introduction to Abstract and Linear Algebra**, [NPTEL](https://nptel.ac.in), IIT Kharagpur.\
  (Prof. Sourav Mukhopadhyay)
  
* 2020 Jan-Aprl **Cryptography and Network Security**, [NPTEL](https://nptel.ac.in), IIT Kharagpur.\
  (Prof. Sourav Mukhopadhyay) 
  
* 2019 Jan-Aprl **Mathematics-II**, department of Mathematics, IIT Kharagpur.\
  (Prof. Pratima Panigrahi)  
  
* 2019 Aug-Oct **Introduction to Abstract and Linear Algebra**, [NPTEL](https://nptel.ac.in), IIT Kharagpur.\
  (Prof. Sourav Mukhopadhyay)  
  
* 2019 Jan-Aprl **Cryptography and Network Security**, [NPTEL](https://nptel.ac.in), IIT Kharagpur.\
  (Prof. Sourav Mukhopadhyay)  
  
* 2018 Aug-Oct **Introduction to Abstract and Linear Algebra**, [NPTEL](https://nptel.ac.in), IIT Kharagpur.\
  (Prof. Sourav Mukhopadhyay)   
  
* 2018 Jan-Aprl **Cryptography and Network Security**, [NPTEL](https://nptel.ac.in), IIT Kharagpur.\
  (Prof. Sourav Mukhopadhyay)  
  
  
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

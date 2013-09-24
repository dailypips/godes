#Godes#

Libriary to Build Discrete Event Simulation Models in Go (http://golang.org/)

Copyright (c) 2013 Alex Goussiatiner agoussia@yahoo.com

###Features###
Godes is the general-purpose simulation library which includes the  simulation engine  and building blocks for modeling a wide variety of systems at varying levels of detail.

###Advantages###
* Godes is easy to learn for the people familiar with the Go and the elementary simulation concept
* Godes model executes fast  as Go compiles to machine code.
* Godes model is multiplatform as Go compiler targets the Linux, Mac OS X, FreeBSD, Microsoft Windows, etc
* Godes model can be embedded in various computer systems and over the network
* Speed of the Godes model compilation is high
* Variety of the IDE with debuggers are available for Go and Godes as well
* The Godes model can use all of the GO's features and libraries
* Code Security - the Godes includes the  source code for the library and Go is an open source project supported by Google
* Godes is free open source software under MIT license

###Installation###
* Download, install and test the Go at your machine. See instructions at http://golang.org/doc/install
* Optionally install one of the free Go IDE (i.e.LiteIDE X)
* Download Godes package
* Test one of the examples provided

###Examples###

####Simulation Case 0. Basic Features####
During the working day the visitors are entering the restaurant at random intervals and immideatly get the table.
The inter arrival interval is the random variable with uniform distribution from 0 to 70 minutes.
The last visitor gets admitted not later than 8 hours after the opening.
The simulation itself is terminated when the last visitors enters the restaurant.
```go
package main

import (
	"fmt"
	"godes"
)

// the arrival is random number generators for the uniform  distribution
var arrival *godes.UniformDistr = godes.NewUniformDistr()

// the Visitor is a Runner
// any type of the Runner should be defined as struct // with the *godes.Runner as anonimous field
type Visitor struct {
	*godes.Runner
	number int
}

var visitorsCount int = 0

func (vst Visitor) Run() { // Any runner should have the Run method
	fmt.Printf("Visitor %v arrives at time= %6.3f \n", vst.number, godes.Stime)

}
func main() {
	var shutdown_time float64 = 8 * 60
	for {
		//godes.Stime is the current simulation time
		if godes.Stime < shutdown_time {
			//the function acivates the Runner
			godes.ActivateRunner(Visitor{&godes.Runner{}, visitorsCount})
			godes.Advance(arrival.Get(0, 70))
			visitorsCount++
		} else {
			break
		}
	}
	godes.WaitUntilDone() // waits for all the runners to finish the Run()
}

Results
Visitor 0 arrives at time=  0.000 
Visitor 1 arrives at time=  6.279 
Visitor 2 arrives at time= 32.193 
Visitor 3 arrives at time= 66.223 
Visitor 4 arrives at time= 122.069 
Visitor 5 arrives at time= 159.754 
Visitor 6 arrives at time= 166.704 
Visitor 7 arrives at time= 189.620 
Visitor 8 arrives at time= 193.194 
Visitor 9 arrives at time= 241.867 
Visitor 10 arrives at time= 252.249 
Visitor 11 arrives at time= 297.207 
Visitor 12 arrives at time= 332.281 
Visitor 13 arrives at time= 341.150 
Visitor 14 arrives at time= 354.308 
Visitor 15 arrives at time= 416.638 
Visitor 16 arrives at time= 449.534 
Visitor 17 arrives at time= 475.263 
```
####Simulation Case 1.  Boolean Controls####
The restaurant has only one table to sit on. During the working day the visitors are entering the restaurant at random intervals
and wait for the table to be available. The inter arrival interval is the random variable with uniform distribution from 0 to 70 minutes.
The time spent in the restaurant is the random variable with uniform distribution from 10 to 60 minutes.
The last visitor gets admitted not later than 8 hours after the opening.
The simulation itself is terminated when the last visitors has left the restaurant.
```go
package main

import (
	"fmt"
	"godes"
)

// the arrival and service are two random number generators for the uniform  distribution
var arrival *godes.UniformDistr = godes.NewUniformDistr()
var service *godes.UniformDistr = godes.NewUniformDistr()

// tableBusy is the boolean control variable than can be accessed and changed by number of Runners
var tableBusy *godes.BooleanControl = godes.NewBooleanControl()

// the Visitor is a Runner
// any type of the Runner should be defined as struct // with the *godes.Runner as anonimous field
type Visitor struct {
	*godes.Runner
	number int
}

var visitorsCount int = 0

func (vst Visitor) Run() { // Any runner should have the Run method
	fmt.Printf("Visitor %v arrives at time= %6.3f \n", vst.number, godes.Stime)
	tableBusy.Wait(false) // this will wait till the tableBusy control becomes false
	tableBusy.Set(true)   // sets the tableBusy control to true - the table is busy
	fmt.Printf("Visitor %v gets the table at time= %6.3f \n", vst.number, godes.Stime)
	godes.Advance(service.Get(10, 60)) //the function advance the simulation time by the value in the argument
	tableBusy.Set(false)               // sets the tableBusy control to false - the table is idle
	fmt.Printf("Visitor %v leaves at time= %6.3f \n", vst.number, godes.Stime)
}
func main() {
	var shutdown_time float64 = 8 * 60
	for {
		//godes.Stime is the current simulation time
		if godes.Stime < shutdown_time {
			//the function acivates the Runner
			godes.ActivateRunner(Visitor{&godes.Runner{}, visitorsCount})
			godes.Advance(arrival.Get(0, 70))
			visitorsCount++
		} else {
			break
		}
	}
	godes.WaitUntilDone() // waits for all the runners to finish the Run()
}
Results
Visitor 0 arrives at time=  0.000 
Visitor 0 gets the table at time=  0.000 
Visitor 0 leaves at time= 36.055 
Visitor 1 arrives at time= 36.477 
Visitor 1 gets the table at time= 36.477 
Visitor 1 leaves at time= 92.411 
Visitor 2 arrives at time= 100.784 
Visitor 2 gets the table at time= 100.784 
Visitor 3 arrives at time= 103.355 
Visitor 2 leaves at time= 112.620 
Visitor 3 gets the table at time= 112.620 
Visitor 4 arrives at time= 168.231 
Visitor 3 leaves at time= 168.960 
Visitor 4 gets the table at time= 168.960 
Visitor 4 leaves at time= 218.210 
Visitor 5 arrives at time= 223.180 
Visitor 5 gets the table at time= 223.180 
Visitor 6 arrives at time= 229.387 
Visitor 5 leaves at time= 237.614 
Visitor 6 gets the table at time= 237.614 
Visitor 7 arrives at time= 242.663 
Visitor 6 leaves at time= 257.096 
Visitor 7 gets the table at time= 257.096 
Visitor 8 arrives at time= 287.359 
Visitor 9 arrives at time= 293.332 
Visitor 7 leaves at time= 299.023 
Visitor 8 gets the table at time= 299.023 
Visitor 8 leaves at time= 313.288 
Visitor 9 gets the table at time= 313.288 
Visitor 10 arrives at time= 317.668 
Visitor 9 leaves at time= 340.672 
Visitor 10 gets the table at time= 340.672 
Visitor 11 arrives at time= 368.852 
Visitor 10 leaves at time= 387.232 
Visitor 11 gets the table at time= 387.232 
Visitor 12 arrives at time= 391.438 
Visitor 11 leaves at time= 413.365 
Visitor 12 gets the table at time= 413.365 
Visitor 13 arrives at time= 459.622 
Visitor 12 leaves at time= 472.067 
Visitor 13 gets the table at time= 472.067 
Visitor 13 leaves at time= 497.555 
```
####Simulation Case 2.  Queues####
During the four working hours the visitors are entering the restaurant at random intervals and form the arrival queue. 
The inter arrival interval is the random variable with uniform distribution from 0 to 30 minutes. The restaurant employs two waiters who are servicing one visitor in a time. The service time  is the random variable with uniform distribution from 10 to 60 minutes. 
The simulation itself is terminated when 
* Simulation time passes the four hours 
* Both waiters have finished servicing  
* There is no visitors in the arrival queue. 

The model  calculates the average (arithmetic mean) of  the visitors waiting time

```go
// Copyright 2013 Alex Goussiatiner. All rights reserved.
// Use of this source code is governed by a MIT
// license that can be found in the LICENSE file.
package main

import (
	"fmt"
	"godes"
)

// the arrival and service are two random number generators for the uniform  distribution
var arrival *godes.UniformDistr = godes.NewUniformDistr()
var service *godes.UniformDistr = godes.NewUniformDistr()

// true when waiter should act
var waitersSwt *godes.BooleanControl = godes.NewBooleanControl()

// FIFO Queue for the arrived
var visitorArrivalQueue *godes.FIFOQueue = godes.NewFIFOQueue()

// the Visitor is a Passive Object
type Visitor struct {
	id int
}

// the Waiter is a Runner
type Waiter struct {
	*godes.Runner
	id int
}

var visitorsCount int = 0
var shutdown_time float64 = 4 * 60

func (waiter Waiter) Run() {

	for {
		waitersSwt.Wait(true)
		if visitorArrivalQueue.Len() > 0 {
			visitor := visitorArrivalQueue.Get()
			if visitorArrivalQueue.Len() == 0 {
				waitersSwt.Set(false)
			}
			fmt.Printf("Visitor %v is invited by waiter %v at %6.3f \n", visitor.(Visitor).id, waiter.id, godes.Stime)
			godes.Advance(service.Get(10, 60)) //advance the simulation time by the visitor service time
			fmt.Printf("Visitor %v leaves at= %6.3f \n", visitor.(Visitor).id, godes.Stime)

		}
		if godes.Stime > shutdown_time && visitorArrivalQueue.Len() == 0 {
			fmt.Printf("Waiter  %v ends the work at %6.3f \n", waiter.id, godes.Stime)
			break
		}

	}
}

func main() {

	var visitor Visitor
	for i := 0; i < 2; i++ {
		godes.ActivateRunner(Waiter{&godes.Runner{}, i})
	}
	for {
		//godes.Stime is the current simulation time
		visitor = Visitor{visitorsCount}
		visitorArrivalQueue.Place(visitor)
		fmt.Printf("Visitor %v arrives at time= %6.3f \n", visitor.id, godes.Stime)
		waitersSwt.Set(true)
		godes.Advance(arrival.Get(0, 30))
		visitorsCount++
		if godes.Stime > shutdown_time {
			break
		}
	}
	waitersSwt.Set(true)
	godes.WaitUntilDone() // waits for all the runners to finish the Run()
	fmt.Printf("Average Waiting Time %6.3f  \n", visitorArrivalQueue.GetAverageTime())
}

Results
Visitor 0 arrives at time=  0.000 
Visitor 0 is invited by waiter 0 at  0.000 
Visitor 1 arrives at time= 12.016 
Visitor 1 is invited by waiter 1 at 12.016 
Visitor 0 leaves at= 30.026 
Visitor 2 arrives at time= 33.032 
Visitor 2 is invited by waiter 0 at 33.032 
Visitor 3 arrives at time= 40.676 
Visitor 2 leaves at= 55.771 
Visitor 3 is invited by waiter 0 at 55.771 
Visitor 1 leaves at= 57.042 
Visitor 4 arrives at time= 58.806 
Visitor 4 is invited by waiter 1 at 58.806 
Visitor 5 arrives at time= 70.037 
Visitor 6 arrives at time= 78.449 
Visitor 7 arrives at time= 81.794 
Visitor 4 leaves at= 87.525 
Visitor 5 is invited by waiter 1 at 87.525 
Visitor 3 leaves at= 95.989 
Visitor 6 is invited by waiter 0 at 95.989 
Visitor 8 arrives at time= 98.902 
Visitor 5 leaves at= 111.545 
Visitor 7 is invited by waiter 1 at 111.545 
Visitor 6 leaves at= 111.563 
Visitor 8 is invited by waiter 0 at 111.563 
Visitor 9 arrives at time= 112.556 
Visitor 10 arrives at time= 117.670 
Visitor 11 arrives at time= 138.034 
Visitor 8 leaves at= 144.320 
Visitor 9 is invited by waiter 0 at 144.320 
Visitor 7 leaves at= 150.059 
Visitor 10 is invited by waiter 1 at 150.059 
Visitor 9 leaves at= 162.843 
Visitor 11 is invited by waiter 0 at 162.843 
Visitor 12 arrives at time= 165.998 
Visitor 13 arrives at time= 187.092 
Visitor 10 leaves at= 193.999 
Visitor 12 is invited by waiter 1 at 193.999 
Visitor 14 arrives at time= 203.380 
Visitor 15 arrives at time= 208.856 
Visitor 11 leaves at= 219.449 
Visitor 13 is invited by waiter 0 at 219.449 
Visitor 16 arrives at time= 231.344 
Visitor 12 leaves at= 239.155 
Visitor 14 is invited by waiter 1 at 239.155 
Visitor 13 leaves at= 256.596 
Visitor 15 is invited by waiter 0 at 256.596 
Visitor 14 leaves at= 258.283 
Visitor 16 is invited by waiter 1 at 258.283 
Visitor 16 leaves at= 284.852 
Waiter  1 ends the work at 284.852 
Visitor 15 leaves at= 304.075 
Waiter  0 ends the work at 304.075 
Average Waiting Time 20.724  
```

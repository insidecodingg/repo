


package main 

import (
	"fmt"
       "github.com/gin-gonic/gin"
	"github.com/jinzhu/gorm"
	_ "github.com/jinzhu/gorm/dialects/mysql"
	_ "gorm.io/gorm"
)

type Websitelogin struct{

  Phonenumber  string	  `form:"phonenumber"`
  Otp           int          `form:"otp"`


}

 // INSERT INTO Websitelogin (phonenumber,otp ) VALUES ('9502440376', '12345');


type  Kaveriexpress  struct {

 Datestarting string 
 Departure    string
 Duration     string
 Arrival      string
 Traintype    string
 Phonenumber    string 
}

 // INSERT INTO Kaveriexpress (datestarting, departure, duration, arrival,traintype) VALUES ('11th jan', '15th jan', '6hrs', '6AM','superfast');

type Coachtype struct {

  Airconditioner  string
  Sleeper         string
  General         string

  }


type Sleeperticketbooking struct {

  Guest           string    `json:"guest"`
  Fair         string        `json:"fair"`
  Age           string       `json:"age"`
  Phonenumber    string     `json:"phonenumber"`
  }


  // INSERT INTO Sleeperticketbooking (guest,fair,age,phonenumber ) VALUES ('mahesh', '12345',21 , 9502440376);



  

var db *gorm.DB

 func Inputvalidation (  req  *gin.Context  )  *Websitelogin{

       var  inputs Websitelogin

        req.Bind(&inputs)

	if len(inputs.Phonenumber) < 10 {
			    req.JSON(201,"invalid password")
			    return nil
	}
  
     return  &inputs

 }


func    APIvalidation (inputs * Websitelogin , req   *gin.Context  ) bool   {

         var responses Kaveriexpress

	 fmt.Println( inputs)

        db.Where("phonenumber = ? AND otp = ?",inputs.Phonenumber ,inputs.Otp).Find(&responses)

   //  db.Where("email = ?", inputs.Email).First(&responses)

       if   responses.Phonenumber == "" {
	 return false
       }

       return true

}


func API( req   *gin.Context   ){

  
 
        inputs:=  Inputvalidation(req   )

	if inputs == nil {
	     req.JSON(201,"invalid input")
              return 
	}
	
	fmt.Println( inputs)

        resp := APIvalidation(  inputs ,req  )

	if resp == false {
	   req.JSON(201,"invalid phonenumber or otp ")
              return 
	}


      var Response  Sleeperticketbooking = Sleeperticketbooking { "ticket confirmed","" ,"","goodnews"}

       req.JSON(200 ,Response)
}



 func init() {

	var err error

	db, err = gorm.Open("mysql", "root:Mahe@786@tcp(127.0.0.1:3306)/mahedb?charset=utf8&parseTime=True")

	if err != nil {
		fmt.Print("jvt", err)
		panic("db not connected")

	}
	db.AutoMigrate(&Kaveriexpress{},Sleeperticketbooking{}  )


}



func main(){

	//  1. server 
	//  2. register api to server 
	 
           r := gin.Default()

     v1 := r.Group("/whereismytrain")
	{

	  v1.GET("/buyticket",API )

	  }
	
	 r.Run(":9999")


}

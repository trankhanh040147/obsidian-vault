`package main`
  
import (  
    "fmt"  
    "time"  
    "github.com/gofiber/fiber/v2/utils")  
  
func main() {  
    // Example ObjectIDs  
    uid := fmt.Sprintf("%s%d", utils.UUIDv4(), time.Now().UnixMilli())  
  
    fmt.Println(uid)  
}`



How to generate uid like this format: 
- QRCode là 1 chuỗi id có độ dài 64 ký tự bao gồm a-zA-Z0-9
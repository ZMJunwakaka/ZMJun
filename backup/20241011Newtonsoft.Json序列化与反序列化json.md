### 1.序列化成json串
```C#
using Newtonsoft.Json;
namespace ConsoleAppTest1011
{
    internal class Program
    {
        static void Main(string[] args)
        {
            Dictionary<string, string> dictionary = ResponseMethod("鲁B16F0H");
            Console.WriteLine(dictionary.ToArray()[0]); 
            Console.WriteLine(dictionary["filter"]); 
        }

        static Dictionary<string, string> ResponseMethod(string plateNo)
        {
            Dictionary<string, string> SGparam = new Dictionary<string, string>
            {
                { "filter",JsonConvert.SerializeObject(
                    new {
                        PlateNo=plateNo
                    }
                    )
                }
            };
            return SGparam;
        }
    }
}
```
![image](https://github.com/user-attachments/assets/80010625-8c64-4e9c-b42a-099b2940b466)

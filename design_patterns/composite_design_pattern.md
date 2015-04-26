#কম্পোজিট ডিজাইন প্যাটার্ন 
মনে করুন, একজন প্রোগ্রামার BFC এর জন্যে একটি সফটওয়্যার ডেভেলপ করার জন্যে চুক্তিবদ্ধ হল । BFC এর সফটওয়্যার এর অনেক মডিউল এর মধ্যে একটি মডিউল হচ্ছে , যে BFC এর মালিক ইচ্ছে করলে কোন জোনে কত টাকার লেনদেন হয়েছে এটা ফিল্টারিং করে দেখতে পারবে । যেমন, ঢাকা শহরে ২০১৫ সালের জুন মাসে BFC এর সবগুলো আউটলেট থেকে মোট কত  টাকা বেচা বিক্রি হয়েছে তা দেখতে পারবে । প্রতিটা জোনে BFC এর  অনেকগুলো আউটলেট/ ষ্টোর থাকতে পারে । যেমন ঢাকা জোনে BFC এর অনেক ষ্টোর রয়েছে।  এখন প্রোগ্রামারকে ঢাকা জোনের প্রতিমাসের মোট বেচাকেনার পরিমাণ বের করতে হলে তাকে প্রতিমাসে ঢাকা জোনের সবগুলো BFC এর আউটলেটের বেচাকেনার হিসেব বের করতে হবে । এজন্যে প্রোগ্রামার প্রতিটা ষ্টোরের হিসেব বের করার জন্যে ষ্টোরগুলোকে এক একটি এনটিটি হিসেবে বিবেচনা করলো । প্রতিটা ষ্টোরের একটি ইউনিক আইডি থাকবে , নাম থাকবে এবং প্রতিমাসে কতটাকা বেচাবিক্রি হয়েছে এটার জন্যে একটা  প্রোপার্টি থাকবে । 
```csharp
    public class Store
    {
        public int Id { get; set; }
        public String Name { get; set; }
        public int Profit { get; set; }
    }
```
 এখন ঢাকা শহরে / জোনের মোট বেচাবিক্রির হিসেব বের করার জন্যে প্রোগ্রামার একটি City class লিখে ফেললো এভাবেঃ 
```csharp
    public class City
    {
        public List<Store> StoreList { get; private set; }
        public int Id { get; set; }
        public String Name { get; set; }

        public City()
        {
            StoreList = new List<Store>();
        }

        public void RemoveStore(Store store)
        {
            StoreList.Remove(store);
        }

        public void AddStore(Store store)
        {
            StoreList.Add(store);
        }

        public int GetCityProfit()
        {
            int totalProfit = 0;
            foreach (Store store in StoreList)
            {
                totalProfit += store.Profit;
            }
            return totalProfit;
        }
    }
```

এখানে দেখা যাচ্ছে যে , City class এ সবগুলো ষ্টোরের জন্যে একটি লিস্ট আছে । 
BFC এর নতুন কোনও আউটলেট হলে লিস্টে নতুন  আউটলেট ঢোকানো অথবা কোনও আউটলেট বন্ধ হয়ে গেলে   লিস্ট থেকে আউটলেট বাদ দেয়ার ব্যবস্থা রাখা হয়েছে । শেষে GetCityProfit() মেথডে City এর টোটাল প্রফিট ক্যালকুলেট করা হয়েছে । সবশেষে ক্লায়েন্ট কোডে ইউজ করা হয়েছে এভাবেঃ
```csharp
    class Program
    {
        public static void Main(string[] args)
        {
            var store1 = new Store
            {
                Id = 1,
                Name = "Malibagh BFC",
                Profit = 2
            };
            var store2 = new Store
            {
                Id = 2,
                Name = "Gulshan BFC",
                Profit = 5
            };

            var city = new City
            {
                Id = 10,
                Name = "Dhaka"
            };
            city.AddStore(store1);
            city.AddStore(store2);
            int totalProfit = city.GetCityProfit();
            Console.WriteLine("Total Profit of {0} city is : {1}", city.Name, totalProfit);
            Console.ReadLine();
        }
    }
```
এখন মনে করুন কিছুদিনের মধ্যে BFC সারাদেশে তাদের ব্যবসা শুরু করলো । বিভাগীয় শহর এবং শহরের জেলা উপজেলা পর্যায়ে আউটলেট চালু হলো । এখন প্রোগ্রামারের উপর দায়িত্ব এসে পড়লো যে তাকে ডিভিশনেরও প্রফিট দেখাতে হবে ।উদাহরন স্বরূপ ,রাজশাহী বিভাগের প্রতিমাসে কত লাভ আসে তাও BFC এর মালিককে দেখাতে হবে । এখন বিভাগের লাভ লোকসান বের করার  জন্যে প্রোগ্রামার City Class মত আরেকটি Division Class বানালো যেটি City Add/Delete করতে পারে এবং  GetDivisionProfit() মেথড এ ডিভিশনের সব City এর টোটাল প্রফিট এর যোগফল বের করে । Division class দেখতে এরকমঃ 
```csharp
    public class Division
    {
        public List<City> CityList { get; private set; }

        public int Id { get; set; }
        public int Profit { get; set; }
        public Division()
        {
            CityList = new List<City>();
        }

        public void AddCity(City city)
        {
            CityList.Add(city);
        }

        public void RemoveCity(City city)
        {
            CityList.Remove(city);
        }

        public int GetDivisionProfit()
        {
            int totalProfit = 0;
            foreach (City city in CityList)
            {
                foreach (Store store in city.StoreList)
                {
                    totalProfit += store.Profit;
                }
            }
            return totalProfit;
        }
    }
```
ডিভিশনের মোট প্রফিট বের করার জন্যে ক্লায়েন্ট কোডে ডিভিশনের ব্যবহার হবে এভাবেঃ
```csharp
    public class Program
    {
        public static void Main(string[] args)
        {
            var store1 = new Store
            {
                Id = 1,
                Name = "Malibagh BFC",
                Profit = 2
            };
            var store2 = new Store
            {
                Id = 2,
                Name = "Gulshan BFC",
                Profit = 5
            };

            var city = new City
            {
                Id = 10,
                Name = "Dhaka"
            };
            city.AddStore(store1);
            city.AddStore(store2);

            var division = new Division
            {
                Id = 1,
                Name = "Rajshahi"
            };

            division.AddCity(city);
            int totalProfit = division.GetDivisionProfit();
            Console.WriteLine("Total Profit of {0} division is : {1}", division.Name, totalProfit);
            Console.ReadLine();
        }
    }
```

প্রোগ্রামার লক্ষ্য করলো যে , প্রতি লেভেলের প্রফিট ক্যালকুলেশনের জন্যে তাকে প্রতিবার একটি করে লুপ বাড়াতে হচ্ছে । আবার সে চিন্তা করে দেখলো যে, এখন সে ডিভিশন লেভেলের প্রফিট ক্যালকুলেট করছে, যদি তাকে এশিয়া রিজিয়নের প্রফিট বের করতে বলে   ( BFC যদি KFC মত ইন্টারন্যাশনাল চেইন শপ হয় কখনও) তাহলে তাকে কয়েকটা দেশের টোটাল প্রফিট বের করতে হবে । সো দেখা যাচ্ছে যে প্রতিটা লেভেলে প্রোগ্রামারকে লুপ বাড়াতে হচ্ছে এবং একটি করে Class যোগ করতে হচ্ছে । আসলে প্রোগ্রামার একটি ট্রি এর মত একটি hierarchy বানিয়ে ফেলেছে । ট্রি এর Division একটি নোড হলে City গুলো হচ্ছে Division এর চিলড্রেন । 

প্রোগ্রামার এতক্ষণ যা করেছে তা হচ্ছে ট্রি hierarchy  এর প্রতিটি নোড এবং এর চাইল্ডের জন্যে একই রকম সব অপারেশন করেছে । (City এর প্রফিট এবং Division এর প্রফিট বের করার তরিকা একই, কিন্তু তাদের রিলেশন কিন্তু প্যারেন্ট -চাইল্ড ) । সো দেখা যাচ্ছে যে , প্রোগ্রামার যদি প্যারেন্ট এবং চাইল্ড কে আলাদা বিবেচনা না করে একইরকম ভাবে ট্রিট করে তাহলেই প্রবলেম সল্ভ হয়ে যাচ্ছে । 
এরকম প্রবলেম সল্ভ করার জন্যেই আসলে কম্পোজিট ডিজাইন প্যাটার্নের আবির্ভাব । 

**রিফ্যাক্টোরিং টু কম্পোজিট ডিজাইন প্যাটার্নঃ**
Store,City ও Division এ দেখা যাচ্ছে যে Add, Remove এবং Calculation এর কাজগুলো বার বার করা হয়েছে । তাই এগুলো কে কমন একটি ইন্টারফেস এ নিয়ে যাওয়া যায় এভাবেঃ
```csharp
    public interface IProfitable
    {
        int GetProfit();
        void AddChild(IProfitable profitable);
        void RemoveChild(IProfitable profitable);
        int Id { get; set; }
        String Name { get; set; }
    }
```
 এখন Store class একে ইমপ্লিমেন্ট করবে এভাবেঃ
 
```csharp
    public class Store : IProfitable
    {
        public int Profit { get; set; }
        public int Id { get; set; }
        public String Name { get; set; }
        public int GetProfit()
        {
            return Profit;
        }
        public void AddChild(IProfitable profitable)
        {
            throw new NotImplementedException();
        }
        public void RemoveChild(IProfitable profitable)
        {
            throw new NotImplementedException();
        }
    }
```
এখানে লক্ষ্যণীয় যে Store class হচ্ছে ট্রি স্ট্রাকচারের Leaf, তাই এর AddChild এবং RemoveChild মেথড Unimplemented অবস্থায় থাকবে । 
একই ভাবে City class IProfitable কে ইমপ্লিমেন্ট করবে এভাবেঃ
```csharp
    public class City : IProfitable
    {
        public int Id { get; set; }
        public String Name { get; set; }
        public List<IProfitable> StoreList { get; private set; }
        public City()
        {
            StoreList = new List<IProfitable>();

        }
        public int GetProfit()
        {
            int totalProfit = 0;

            foreach (IProfitable store in StoreList)
            {
                totalProfit += store.GetProfit();
            }

            return totalProfit;
        }

        public void AddChild(IProfitable profitable)
        {
            if (profitable is Store)
                StoreList.Add(profitable);

        }
        public void RemoveChild(IProfitable profitable)
        {
            if (profitable is Store)
                StoreList.Remove(profitable);
        }
    }
```
এখানে লক্ষ্যণীয় যে ,City class এর AddChild এবং RemoveChild মেথডে আমরা টাইপ চেক করে লিস্টে অবজেক্ট ঢুকিয়েছি কেননা , City class শুধুমাত্র Store অবজেক্ট নিয়ে কাজ করবে । অন্যদিকে Division  class শুধুমাত্র City অবজেক্ট নিয়ে কাজ করবে এজন্যে Division class হবে এরকমঃ
```csharp
    public class Division : IProfitable
    {
        public int Id { get; set; }
        public String Name { get; set; }
        public List<IProfitable> CityList { get; private set; }
        public Division()
        {
            CityList = new List<IProfitable>();
        }
        public int GetProfit()
        {
            int totalProfit = 0;
            foreach (IProfitable city in CityList)
            {
                totalProfit += city.GetProfit();
            }
            return totalProfit;
        }
        public void AddChild(IProfitable profitable)
        {
            if (profitable is City)
                CityList.Add(profitable);
        }
        public void RemoveChild(IProfitable profitable)
        {
            if (profitable is City)
                CityList.Remove(profitable);
        }
    }
```
City class এবং Division class থেকে দেখা যাচ্ছে যে , এই দুটো Class অনেকটা একই রকমের , তাদের মধ্যে পার্থক্য শুধুমাত্র GetProfit() মেথডে । এই মেথডে এখন আর কোনও মাল্টিপল লুপ নেই । আলাদা আলাদা Class এর GetProfit() মেথডের বিজনেস লজিক আলাদা আলাদা এবং এদের মধ্যে কোনও ডিপেন্ডেন্সি নেই ।   সবশেষে ক্লায়েন্ট কোডে প্রফিট ক্যালকুলেট করবে এভাবেঃ
```csharp
    public class Program
    {
        public static void Main(string[] args)
        {
            IProfitable store1 = new Store
            {
                Id = 1,
                Name = "Malibagh BFC",
                Profit = 2
            };
            IProfitable store2 = new Store
            {
                Id = 2,
                Name = "Gulshan BFC",
                Profit = 5
            };

            IProfitable city = new City
            {
                Id = 10,
                Name = "Dhaka"
            };
            city.AddChild(store1);
            city.AddChild(store2);

            IProfitable division = new Division
            {
                Id = 1,
                Name = "Rajshahi"
            };
            division.AddChild(city);
            int totalProfit = division.GetProfit();
            Console.WriteLine("Total Profit of {0} division is : {1}", division.Name, totalProfit);
            Console.ReadLine();
        }
    }
```
সো এইছিলো আমাদের কম্পোজিট ডিজাইন প্যাটার্ন । 
হ্যাপি কোডিং :) 

       
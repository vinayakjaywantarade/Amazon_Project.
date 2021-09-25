package Final_Project;
import java.io.BufferedReader;

import java.io.File;
import java.io.FileInputStream;
import java.io.FileReader;
import java.io.FileWriter;
import java.io.IOException;
import java.io.PrintWriter;
import java.text.DateFormat;
import java.text.SimpleDateFormat;
import java.time.LocalDateTime;
import java.util.ArrayList;
import java.util.Date;
import java.util.Iterator;
import java.util.List;
import java.util.concurrent.TimeUnit;

import org.apache.poi.xssf.usermodel.XSSFSheet;
import org.apache.poi.xssf.usermodel.XSSFWorkbook;
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;public class AmazonTesting {
	
	public static WebDriver driver=null;

		public static void main(String[] args) throws InterruptedException{

			System.setProperty("webdriver.chrome.driver","D:\\Final_Project\\chromedriver.exe");	
			
			driver=new ChromeDriver();
			driver.manage().window().maximize();
			String FilePath="D:\\Final_Project\\ReadProduct.xlsx";
			String FileExtension=".txt";
			DateFormat df=new SimpleDateFormat("dd-M-yyyy HH:mm:ss");
			//DateTimeFormatter df=DateTimeFormatter.ofPattern("yyyy/MM/dd HH:mm:ss");
			LocalDateTime now=LocalDateTime.now();
			Date date=new Date();
			System.out.println(df.format(date));
			//System.out.println(df.format(now));
			String CurrentDateTime=df.format(date);
			String CurrentTime=CurrentDateTime.replaceAll("\\s", "");
			System.out.println(FilePath+CurrentTime.replaceAll(":", "-")+FileExtension);
			try(PrintWriter writer = new PrintWriter(new FileWriter(new File(FilePath+CurrentTime.replaceAll(":", "-")+FileExtension)));
				BufferedReader reader = new BufferedReader(new FileReader(new File("D:\\Final_Project\\ReadFile.txt")))){
				List<String> list = new ArrayList<>();
				reader.lines().forEach(l->list.add(l));
				Iterator<String> itr = list.iterator();
				if(itr.hasNext()) {
					String homepage =itr.next();
					driver.navigate().to(homepage);
					driver.manage().timeouts().implicitlyWait(10,TimeUnit.SECONDS);
					
					WebElement signIn=driver.findElement(By.id("nav-link-accountList-nav-line-1"));
					signIn.click();
					WebElement UserName=driver.findElement(By.id("ap_email"));
					UserName.sendKeys("7447390331");
					WebElement Continue=driver.findElement(By.id("continue"));
					Continue.click();
					WebElement Passward=driver.findElement(By.id("ap_password"));
					Passward.sendKeys("Mayuri@24");
					WebElement Submit=driver.findElement(By.id("signInSubmit"));
					Submit.click();
				
					File src = new File("D:\\Final_Project\\ReadProduct.xlsx");
					
					FileInputStream stream = new FileInputStream(src);
					XSSFWorkbook workbook = new XSSFWorkbook(stream);
					XSSFSheet sheet = workbook.getSheetAt(0);
				
					for(int i=0;i<3;i++) {
					
						searchProduct(sheet.getRow(i).getCell(0).getStringCellValue(), writer);
						
					}
					driver.navigate().to(homepage);
					workbook.close();
				}
				else {
					System.out.println("Enter Login Details Correctly");
				}
			}catch(Exception e) {
				e.printStackTrace();
				System.out.println("File Not Present");
			}
			
		}

		public static void searchProduct(String productName,PrintWriter writer) {
			System.out.println(productName);
			WebElement searchText =  driver.findElement(By.id("twotabsearchtextbox"));
			searchText.sendKeys(productName);
			driver.findElement(By.id("nav-search-submit-button")).click();
			List<WebElement> list = driver.findElements(By.xpath("//span[@class='a-size-medium a-color-base a-text-normal']"));
			if(list.isEmpty()) {
				 list=driver.findElements(By.xpath("//span[@class='a-size-base-plus a-color-base a-text-normal']"));
			}
			list.addAll(driver.findElements(By.xpath("//span[@class='a-size-medium a-color-base a-text-normal']")));
			Iterator<WebElement> itr = list.iterator();
			int i=1;
			writer.println(productName.toUpperCase()+"-");
			while(itr.hasNext() && i<=5) {
				String result =itr.next().getText();
//				prints top Five result on console
				System.out.println("Result "+i+"-> "+result);
//				writes top Five result into text file
				writer.println("Result "+i+" -> "+result);
				i++;
			}
			searchText =  driver.findElement(By.id("twotabsearchtextbox"));
			searchText.clear();
			writer.println("");
		}


		
	}
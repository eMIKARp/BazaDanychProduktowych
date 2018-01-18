# BazaDanychProduktowych

    package bazadanychproduktowych;

    import java.io.*;
    import java.util.logging.Level;
    import java.util.logging.Logger;
    import java.io.*;
    import java.util.*;

    public class Main 
    {

   
    public static void main(String[] args) 
    
    {
        Towar[] towar = new Towar[10];
        
            towar[0] = new Towar("Towar 0", 10.0, 2017, 1, 1);
            towar[1] = new Towar("Towar 1", 11.0, 2017, 2, 2);
            towar[2] = new Towar("Towar 2", 12.0, 2017, 3, 3);
            towar[3] = new Towar("Towar 3", 13.0, 2017, 4, 4);
            towar[4] = new Towar("Towar 4", 14.0, 2017, 5, 5);
            towar[5] = new Towar("Towar 5", 15.0, 2017, 6, 6);
            towar[6] = new Towar("Towar 6", 16.0, 2017, 7, 7);
            towar[7] = new Towar("Towar 7", 17.0, 2017, 8, 8);
            towar[8] = new Towar("Towar 8", 18.0, 2017, 8, 9);
            towar[9] = new Towar("Towar 9", 19.0, 2017, 10, 10);
                         
        try 
        {
                 
            RandomAccessFile RAF = new RandomAccessFile("BazaProduktow.txt", "rw");
            Towar.zapiszDoPliku(towar, RAF);
            RAF.seek(0);
                       
            
            Towar[] towarki = Towar.odczytajZPliku(RAF);
            for (int i = 0; i < towarki.length; i++)
            {
                System.out.println(towarki[i].pobierzCene());
                System.out.println(towarki[i].pobierzNazwe());
                System.out.println(towarki[i].pobierzDateWydania());
                System.out.println("");
                System.out.println("------------------------------------------");
            }
           
            
            try {
                Towar b = new Towar();
                b.odczytajRekord(RAF, 1);
                System.out.println(b);
            } 
            catch (BrakRekordu ex) {
                System.out.println(ex.getMessage());
            }
            
            RAF.close();
            
            
            
        }
        catch (IOException ex)
        {
            System.out.println("Wystąpił błąd: " + ex.getMessage());
        }
            
    }
    
    public class Towar 

    {
    private double cena;
    private String nazwa;
    private Date dataWydania;
    public static final int DLUGOSC_NAZWY = 30;
    public static final int DLUGOSC_REKORU = (Character.SIZE*DLUGOSC_NAZWY + Double.SIZE + 3 * Integer.SIZE)/8;
    
    public Towar()
    {
        this.cena = 0.0;
        this.nazwa = " ";
        this.dataWydania = new GregorianCalendar().getTime();
                
    }
    
    public Towar(String nazwa, double cena)
    {
        this();
        this.cena = cena;
        this.nazwa = nazwa;
                    
    }
    
    public Towar(String nazwa, double cena, int rok, int miesiac, int dzien)
    {
        this(nazwa, cena);
        GregorianCalendar kalendarz = new GregorianCalendar(rok, miesiac -1, dzien);
        dataWydania = kalendarz.getTime();
                    
    }
    
    public double pobierzCene()
    {
        return this.cena;
        
    }
    
    public void ustawCene(double cena)
    {
        this.cena = cena;
        
    }
    
    public String pobierzNazwe()
    {
        return this.nazwa;
        
    }
    
    public void ustawNazwe(String nazwa)
    {
       this.nazwa = nazwa;
        
    }
    
    public Date pobierzDateWydania()
    {
        return this.dataWydania;
        
    }
    
    public void ustawDateWydania(int rok, int miesiac, int dzien)
    {
        GregorianCalendar kalendarz = new GregorianCalendar(rok, miesiac -1, dzien);
        this.dataWydania = kalendarz.getTime();
        
    }
    
    public void ustawDateWydania(Date dataWydania)
    {
        this.dataWydania = dataWydania;
        
    }
    
    @Override
    public String toString()
    {
        GregorianCalendar kalendarz = new GregorianCalendar();
        kalendarz.setTime(this.dataWydania);
        return "Nazwa produktu: " + this.nazwa + ", cena produktu: " + this.cena + ", data wydania produktu: " + kalendarz.get(Calendar.YEAR) + " " + (kalendarz.get(Calendar.MONTH)+1) + " " + kalendarz.get(Calendar.DAY_OF_MONTH);
    }
    
    public static void zapiszDoPliku( Towar[] towar, DataOutput outS) throws IOException
    {
        for (int i = 0; i < towar.length; i++)
        {
            towar[i].zapiszDane(outS);
        }
    }
    
    
    public static Towar[] odczytajZPliku(RandomAccessFile RAF) throws IOException
    {
        int ilRekordow = (int)RAF.length()/DLUGOSC_REKORU;
        Towar[] towar = new Towar[ilRekordow];
    
        for (int i = 0; i < ilRekordow; i++)
        {
           towar[i] = new Towar();
           towar[i].odczytajDane(RAF);
        }
        
        return towar;
    }
    
    public void zapiszDane(DataOutput outS) throws IOException
    {
        StringBuffer stringB = new StringBuffer(DLUGOSC_NAZWY);
        stringB.append(this.nazwa);
        stringB.setLength(DLUGOSC_NAZWY);
        outS.writeChars(stringB.toString());
       
        outS.writeDouble(cena);
        
        GregorianCalendar kalendarz = new GregorianCalendar();
        kalendarz.setTime(this.dataWydania);
        
        outS.writeInt(kalendarz.get(Calendar.YEAR));
        outS.writeInt(kalendarz.get(Calendar.MONTH) + 1);
        outS.writeInt(kalendarz.get(Calendar.DAY_OF_MONTH));
       
    }
    
    public void odczytajDane(DataInput inS) throws IOException
    {
        
        StringBuffer stringB = new StringBuffer(Towar.DLUGOSC_NAZWY);
        
        for(int i = 0; i < Towar.DLUGOSC_NAZWY; i++)
        {
            char tCh = inS.readChar();
            if (tCh != '\0')
            stringB.append(tCh);
        }
        this.nazwa = stringB.toString();
        this.cena = inS.readDouble();
        
        int rok = inS.readInt();
        int miesiac = inS.readInt();
        int dzien = inS.readInt();
        
        GregorianCalendar kalendarz = new GregorianCalendar(rok, miesiac - 1, dzien);
        this.dataWydania = kalendarz.getTime();
    }
    
    public void odczytajRekord(RandomAccessFile RAF, int n) throws IOException, BrakRekordu
    {
        if (n <= RAF.length()/Towar.DLUGOSC_REKORU)
        {
            RAF.seek((n-1)*Towar.DLUGOSC_REKORU);
            this.odczytajDane(RAF);
        }
        else throw new BrakRekordu("Nie ma takiego rekordu");
        
    }
    
    }
    
    public class BrakRekordu extends Exception 
    {
    public BrakRekordu()
    {
        super();
    }
    public BrakRekordu(String error)
    {
        super(error);
    }
    }

    }

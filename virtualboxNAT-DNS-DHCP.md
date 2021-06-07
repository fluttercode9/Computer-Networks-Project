# Charakterystyka:

Do rozwiązania problemu organizacji wykorzystałem usługę DNS, bramę NAT, serwer DHCP zainstalowane na jednym urządzeniu, które pełni też funkcję routera. Symulację tego rozwiązania przeprowadziłem w oprogramowaniu Virtualbox na systemie operacyjnym Alpine Linux. Od teraz każde nowe urządzenie automatycznie otrzyma adres IP w tej sieci, połączy się z WAN z pomocą bramy NAT oraz uzyska dostęp do drukarki, erp i routera za pomocą nazwy domeny poprzez usługę DNS. 

sieć - 192.168.0.0 maska: 255.255.252.0(/22)

# konfiguracja interfejsów naszego routera/dns/dhcp/nat

* eth0 : 
  * NAT
  * 10.0.2.15/24

* eth1 : 
  * sieć NAT
  * 192.168.0.1/22


# DHCP:

Na naszym routerze pobieramy usługę DHCP:

 ``` apk add dhcp ```

dodajemy konfigurację dla DHCP, edytujemy plik ``` /etc/dhcp/dhcpd.conf ```  

```
subnet 192.168.0.0 255.255.252.0{
  range 192.168.0.10 192.168.3.254;
  option routers 192.168.0.1;
  option domain-name-servers 192.168.0.1; }

host printer01 {
	hardware ethernet          08:00:27:dc:0e:5a;
	fixed-address              192.168.1.101;
  option routers             192.168.0.1;
  option domain-name-servers 192.168.0.1;
}

host server01 {
	hardware ethernet          08:00:27:dc:0e:5a;
	fixed-address              192.168.0.2;
  option routers             192.168.0.1;
  option domain-name-servers 192.168.0.1;
}


```
Zostawiłem miejsce w puli adresów na dodatkowych hostów ze statycznym adresem IP, w razie gdyby organizacja chciała dołączyć np. nową drukarkę lub serwer.
Każdy nowy host dostanie również informację gdzie leży brama oraz DNS.
Zdefiniowane zostały również statyczne adresy IP na podstawie adresów MAC interfejsów. 

# DNS

instalujemy usluge dns na routerze:

  ``` apk add dnsmasq ``` 

edytujemy plik ```/etc/hosts``` :
```
192.168.0.3    erp.mojaorganizacja.pl
192.168.0.1    router.mojaorganizacja.pl
192.168.0.2    drukarka.mojaorganizacja.pl

```
Od teraz możemy posługiwać się nazwami domen, możemy sprawdzić czy wszystko działa na naszych klientach:
```
ping erp.mojaorganizacja.pl
nslookup drukarka.mojaorganizacja.pl 
```


# NAT:

domyslnie nasz pc ktory pelni rolę routera nie jest skonfigurowany do przekazywania pakietów między interfejsami.
Aby stało się to możliwe:

```echo 1 > /proc/sys/net/ipv4/ip_forward```

teraz musimy zapewnić translację adresów prywatnych naszych hostów na takie które będą mogły poruszać się po sieci rozległej i do nas wrócić.
pobieramy program iptables (nadpisuje adres zrodlowy adresem interfejsu WAN)

```apk add iptables```

następnie:

``` iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE```

dla wszystkich pakietów wychodzących z eth0 nakłada adres, a gdy pakiet wraca, następuje translacja zwrotna na adres prywatny.
Od teraz komunikacja z siecią rozległą przez hosty w naszej sieci lokalnej jest możliwa.



możemy również dodać adres serwera DNS do naszego routera aby klienci mogli posługiwać się nazwami domen w sieci WAN. -> ```/etc/resolv.conf``` wspisujemy
``` nameserver 8.8.8.8 ```



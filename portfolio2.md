## Docker Networking

Door gebruik te maken van docker networking is het mogelijk om flexibele netwerk configuraties te maken binnen de Docker host.
In dit geval wordt het gebruik gemaakt van een networkbridge.

Networking bridging maakt het mogelijk om subnetten te maken die elkaar standaard niet kunnen bereiken.
Dit is handig om verschillende containers van elkaar te kunnen scheiden, bijvoorbeeld een productie / development omgeving.

In mijn situatie is er gebruik gemaakt van 3 bridges:

bridge_00    20.24.21.0/24
bridge_01    21.24.21.0/24
bridge_lb    22.24.21.0/24

### Commands voor het maken van de bridges

    docker network create --driver=bridge --subnet=20.24.21.0/24 --ip-range=20.24.21.0/24 --gateway=20.24.21.1 bridge_00
    docker network create --driver=bridge --subnet=21.24.21.0/24 --ip-range=21.24.21.0/24 --gateway=21.24.21.1 bridge_01
    docker network create --driver=bridge --subnet=22.24.21.0/24 --ip-range=22.24.21.0/24 --gateway=22.24.21.1 bridge_lb

Deze kunnen niet met elkaar praten maar de onderliggende host kan wel alle hosts op alle bridges bereiken.

## NGINX Loadbalancing / Reverse proxy

Een simpele reverse proxy maakt meerder servers met verschillende IP adressen beschikbaar onder 1 IP adres. Een reverse proxy kan echter ook fungeren als loadbalancer, cache en beveiligingslaag voor een schaalbare infrastructuur.
Als er geen algoritme wordt gekozen wordt er automatisch gebruikt gemaakt van round robin. d.w.z. bij elke aanvraag wordt de volgende server gekozen; nginx_1, nginx_2, nginx_3, nginx_1.. etc.


NGINX wordt gebruikt als loadbalancing (nginx_lb) voor de 2 webservers; nginx_1 en nginx_2

https://youtu.be/Q8BYaq5SM8M < Zie het in actie

## Container starting commands

    docker run --privileged --name nginx_1 --ip 20.24.21.10 --network bridge_00 -p 80:80 -itd --rm nginx
    docker run --privileged --name nginx_2 --ip 21.24.21.10 --network bridge_01 -p 81:80 -itd --rm nginx
    docker run --privileged --name nginx_lb --ip 22.24.21.10 --network bridge_02 -p 8080:80 -itd --rm nginx

    



Geraadpleegde bronnen:

https://dev.to/mazenr/how-to-implement-a-load-balancer-using-nginx-docker-4g73

https://docs.docker.com/engine/network/drivers/bridge/

https://www.theserverside.com/blog/Coffee-Talk-Java-News-Stories-and-Opinions/Docker-Nginx-reverse-proxy-setup-example

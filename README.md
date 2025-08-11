# clima
aplicativo de ver clima
import tkinter
from tkinter import *
from tkinter import ttk

# Importando bibliotecas
from PIL import Image, ImageTk

#importando bibliotecas
import requests
from datetime import datetime
import json


import pytz
import pycountry_convert as pc

# a linha abaixo serão as cores
co0 = '#4444466' # Preta
co1 = '#feffff'  # Branca   
co2 = '#38576b'  # Azul

fundo_dia = '#6ccacc'
fundo_noite = '#484f60'
fundo_tarde = '#b8cfe5'

fundo = fundo_dia 

janela = Tk()
janela.title('Clima')
janela.geometry('320x350')
janela.configure(bg=fundo)

ttk.Separator(janela, orient=HORIZONTAL).grid(row=0, columnspan=1, ipadx=157 )


# criando os frames
frame_top = Frame(janela, width=320, height=50, bg=co1, pady=0, padx=0 )
frame_top.grid(row=1, column=0)


frame_corpo = Frame(janela, width=320, height=300, bg=fundo, pady=12, padx=0 )
frame_corpo.grid(row=2, column=0,sticky=NW)

estilo = ttk.Style(janela)
estilo.theme_use('clam')

global imagem

# função que retorna as informações do clima

def informacao():
    
    chave = '414a540e797be141aef3846084f42bfb'
    cidade = e_local.get()
    api_link = "https://api.openweathermap.org/data/2.5/weather?q={}&appid={}".format(cidade,chave)

    # Obtendo dados da API do request
    r = requests.get(api_link)


    # Convertendo dados na variável r em dicionário
    dados = r.json()

    print (dados)

   #obtendo dados da zona,pais e horas

    pais_codigo = dados['sys']['country']



   #---- zona ----
    zona_fuso= pytz.country_timezones[pais_codigo]


   #---- pais ----
    pais= pytz.country_names[pais_codigo]


   #---- data ----

    zona= pytz.timezone(zona_fuso[7])
    zona_horas = datetime.now(zona)
    zona_horas = zona_horas.strftime('%d %m %y | %H:%M:%S %p')



   #---- tempo ----

    tempo = dados['main']['temp']
    pressao= dados['main']['pressure']
    humidade = dados['main']['humidity']
    velocidade = dados['wind']['speed']
    descricao = dados['weather'][0]['description']




   #mandando informações

    def pais_para_continente(i):
      pais_alpha = pc.country_name_to_country_alpha2(i)
      pais_continent_codigo = pc.country_alpha2_to_continent_code(pais_alpha)
      pais_continent_name = pc.convert_continent_code_to_continent_name(pais_continent_codigo)
    
      return pais_continent_name

    continente= pais_para_continente(pais)


    #passando as informações nas Labels
    l_cidade['text'] = cidade + ' - ' + pais + ' / ' + continente
    l_data['text'] = zona_horas
    l_humidade['text'] = humidade
    l_h_simbolo['text'] = '%'
    l_h_nome['text'] = 'Umidade'
    l_Pressão['text'] = 'Pressão: ' + str(pressao)
    l_velocidade['text'] = 'Velocidade do vento: ' + str(velocidade)
    l_descricao['text'] = descricao


    #logica para trocar o fundo e o icone

    zona_periodo = datetime.now(zona)
    zona_periodo = zona_periodo.strftime('%H')


    global imagem

    zona_periodo = int(zona_periodo)

    if zona_periodo <= 6:
       imagem = Image.open('images/noite.png')
       fundo = fundo_noite

    elif zona_periodo <= 11:
       imagem = Image.open('images/dia.png')
       fundo = fundo_dia
       
    elif zona_periodo <= 17:
       imagem = Image.open('images/por do sol.png')
       fundo = fundo_tarde
       
    elif zona_periodo <= 23:
       imagem = Image.open('images/noite.png')
       fundo = fundo_noite
       
    else:
       pass
    



      

    imagem = imagem.resize((130, 130))
    imagem = ImageTk.PhotoImage(imagem)

    l_icone=Label (frame_corpo,image=imagem, bg=fundo)
    l_icone.place(x=162, y=50)

    janela.configure(bg=fundo)
    frame_top.configure(bg=fundo)
    frame_corpo.configure(bg=fundo)



    l_cidade['bg'] = fundo             
    l_data['bg'] = fundo
    l_humidade['bg'] = fundo
    l_h_simbolo['bg'] = fundo        
    l_h_nome['bg'] = fundo
    l_Pressão['bg'] = fundo
    l_velocidade['bg'] = fundo
    l_descricao['bg'] = fundo
       

   




#comfigurando o top

e_local= Entry(frame_top, width=20, justify="left", font=("", 14),highlightthickness=1, relief='solid')
e_local.place(x=15, y=10)
b_ver= Button(frame_top, command=informacao, text="ver clima", bg=co1, fg=co2, font=(" Ivy 9 bold"),relief='raised', overrelief=RIDGE)
b_ver.place(x=250, y=10)


#comfigurando o corpo

l_cidade=Label (frame_corpo, text= "", anchor="center", bg=fundo, fg=co1, font=("Arial 14"))
l_cidade.place(x=10, y=4)

l_data=Label (frame_corpo, text="", anchor="center", bg=fundo, fg=co1, font=("Arial 10"))
l_data.place(x=10, y=54)

l_humidade=Label (frame_corpo, text="", anchor="center", bg=fundo, fg=co1, font=("Arial 45"))
l_humidade.place(x=10, y=100)

l_h_simbolo=Label (frame_corpo, text="", anchor="center", bg=fundo, fg=co1, font=("Arial 10 bold"))
l_h_simbolo.place(x=85, y=110)

l_h_nome=Label (frame_corpo, text="", anchor="center", bg=fundo, fg=co1, font=("Arial 8"))
l_h_nome.place(x=85, y=140)


l_Pressão=Label (frame_corpo, text="", anchor="center", bg=fundo, fg=co1, font=("Arial 10"))
l_Pressão.place(x=10, y=184)

l_velocidade=Label (frame_corpo, text="", anchor="center", bg=fundo, fg=co1, font=("Arial 10"))
l_velocidade.place(x=10, y=212)
 


l_descricao=Label (frame_corpo, text="", anchor="center", bg=fundo, fg=co1, font=("Arial 10"))
l_descricao.place(x=100, y=190)



janela.mainloop()

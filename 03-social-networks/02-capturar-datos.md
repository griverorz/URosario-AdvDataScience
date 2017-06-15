Para crear nuestro primer análisis de redes real, usaremos datos de las
redes de colaboración en el Congreso de los Diputados de España. Esto
nos servirá para ver en la práctica el tipo de decisiones que tenemos
que tomar a la hora de construir la red.

    library(jsonlite)

    ## Loading required package: methods

    library(httr)

Podemos empezar por extraer los datos de la lista de diputados

    URL <- 'http://api.quehacenlosdiputados.net/diputados'
    diputados <- GET(URL)
    diputados <- content(diputados, as="text")
    diputados <- fromJSON(diputados, simplifyVector=FALSE)

A continuación tomamos la lista de diputados y extraeremos su
identificador único.

    list_diputados <- lapply(diputados, function(x) x$id)

Recordemos que lo que nos interesa es capturar las leyes en las que cada
diputado a trabajado y cuáles son los coautores. La información sobre
las iniciativas legislativas en las que ha participado cada diputado
está disponible en el *endpoint* `iniciativas` que es hijo de la `id` de
cada `diputado`. Por tanto, lo que haremos será crear una función que,
para cada `id` recoja la información sobre las iniciativas asociadas a
ese candidato.

    get_iniciativas <- function(x) {
        url <- sprintf("http://api.quehacenlosdiputados.net/diputado/%s/iniciativas", x)
        dip_data <- GET(url)
        ## Estamos haciendo una recogida muy breve de datos
        ## En recogidas mas grandes querremos
        ## 1. verificar que el contenido no es vacio 
        ## 2. Guardar en disco a medida que capturamos datos
        ## 3. Tomar acciones si recibimos error o si falla la conexion
        out <- fromJSON(content(dip_data, as="text"), simplifyVector=FALSE)
        return(out)
    }

Ahora, crearemos un espacio para poder almacenar estos datos. Lo haremos
en una lista, así que podemos aprovechar los nombres de la lista para
poder mantener un poco de orden en la información recogida.

    collab <- vector("list", length(list_diputados))
    names(collab) <- unlist(list_diputados)
    head(collab)

    ## $`171`
    ## NULL
    ## 
    ## $`131`
    ## NULL
    ## 
    ## $`233`
    ## NULL
    ## 
    ## $`7`
    ## NULL
    ## 
    ## $`320`
    ## NULL
    ## 
    ## $`187`
    ## NULL

Ahora, iremos sobre cada una de las `id` que hemos recogido antes y
recogeremos los datos relativos a las iniciativas de cada diputado. Para
no sobrecargar el servidor, dejaremos un espacio de un segundo entre
llamadas. Además, imprimiremos información a medida que la descarga vaya
avanzando para asegurarnos de que no hay ningún problema:

    for (i in names(collab)) {
        remaining <- length(collab) - which(names(collab) == i)
        print(sprintf("Capturando los datos de id=%s. Faltan %s llamadas", i, remaining))
        collab[[i]] <- get_iniciativas(i)
        ## Sys.sleep(1)
    }

    ## [1] "Capturando los datos de id=171. Faltan 349 llamadas"
    ## [1] "Capturando los datos de id=131. Faltan 348 llamadas"
    ## [1] "Capturando los datos de id=233. Faltan 347 llamadas"
    ## [1] "Capturando los datos de id=7. Faltan 346 llamadas"
    ## [1] "Capturando los datos de id=320. Faltan 345 llamadas"
    ## [1] "Capturando los datos de id=187. Faltan 344 llamadas"
    ## [1] "Capturando los datos de id=84. Faltan 343 llamadas"
    ## [1] "Capturando los datos de id=255. Faltan 342 llamadas"
    ## [1] "Capturando los datos de id=359. Faltan 341 llamadas"
    ## [1] "Capturando los datos de id=276. Faltan 340 llamadas"
    ## [1] "Capturando los datos de id=211. Faltan 339 llamadas"
    ## [1] "Capturando los datos de id=247. Faltan 338 llamadas"
    ## [1] "Capturando los datos de id=279. Faltan 337 llamadas"
    ## [1] "Capturando los datos de id=22. Faltan 336 llamadas"
    ## [1] "Capturando los datos de id=361. Faltan 335 llamadas"
    ## [1] "Capturando los datos de id=201. Faltan 334 llamadas"
    ## [1] "Capturando los datos de id=102. Faltan 333 llamadas"
    ## [1] "Capturando los datos de id=132. Faltan 332 llamadas"
    ## [1] "Capturando los datos de id=37. Faltan 331 llamadas"
    ## [1] "Capturando los datos de id=296. Faltan 330 llamadas"
    ## [1] "Capturando los datos de id=198. Faltan 329 llamadas"
    ## [1] "Capturando los datos de id=299. Faltan 328 llamadas"
    ## [1] "Capturando los datos de id=55. Faltan 327 llamadas"
    ## [1] "Capturando los datos de id=286. Faltan 326 llamadas"
    ## [1] "Capturando los datos de id=134. Faltan 325 llamadas"
    ## [1] "Capturando los datos de id=223. Faltan 324 llamadas"
    ## [1] "Capturando los datos de id=220. Faltan 323 llamadas"
    ## [1] "Capturando los datos de id=317. Faltan 322 llamadas"
    ## [1] "Capturando los datos de id=231. Faltan 321 llamadas"
    ## [1] "Capturando los datos de id=205. Faltan 320 llamadas"
    ## [1] "Capturando los datos de id=47. Faltan 319 llamadas"
    ## [1] "Capturando los datos de id=68. Faltan 318 llamadas"
    ## [1] "Capturando los datos de id=33. Faltan 317 llamadas"
    ## [1] "Capturando los datos de id=64. Faltan 316 llamadas"
    ## [1] "Capturando los datos de id=225. Faltan 315 llamadas"
    ## [1] "Capturando los datos de id=251. Faltan 314 llamadas"
    ## [1] "Capturando los datos de id=341. Faltan 313 llamadas"
    ## [1] "Capturando los datos de id=294. Faltan 312 llamadas"
    ## [1] "Capturando los datos de id=181. Faltan 311 llamadas"
    ## [1] "Capturando los datos de id=176. Faltan 310 llamadas"
    ## [1] "Capturando los datos de id=311. Faltan 309 llamadas"
    ## [1] "Capturando los datos de id=67. Faltan 308 llamadas"
    ## [1] "Capturando los datos de id=258. Faltan 307 llamadas"
    ## [1] "Capturando los datos de id=127. Faltan 306 llamadas"
    ## [1] "Capturando los datos de id=28. Faltan 305 llamadas"
    ## [1] "Capturando los datos de id=19. Faltan 304 llamadas"
    ## [1] "Capturando los datos de id=108. Faltan 303 llamadas"
    ## [1] "Capturando los datos de id=91. Faltan 302 llamadas"
    ## [1] "Capturando los datos de id=219. Faltan 301 llamadas"
    ## [1] "Capturando los datos de id=236. Faltan 300 llamadas"
    ## [1] "Capturando los datos de id=237. Faltan 299 llamadas"
    ## [1] "Capturando los datos de id=141. Faltan 298 llamadas"
    ## [1] "Capturando los datos de id=242. Faltan 297 llamadas"
    ## [1] "Capturando los datos de id=310. Faltan 296 llamadas"
    ## [1] "Capturando los datos de id=362. Faltan 295 llamadas"
    ## [1] "Capturando los datos de id=358. Faltan 294 llamadas"
    ## [1] "Capturando los datos de id=17. Faltan 293 llamadas"
    ## [1] "Capturando los datos de id=278. Faltan 292 llamadas"
    ## [1] "Capturando los datos de id=31. Faltan 291 llamadas"
    ## [1] "Capturando los datos de id=156. Faltan 290 llamadas"
    ## [1] "Capturando los datos de id=40. Faltan 289 llamadas"
    ## [1] "Capturando los datos de id=298. Faltan 288 llamadas"
    ## [1] "Capturando los datos de id=271. Faltan 287 llamadas"
    ## [1] "Capturando los datos de id=224. Faltan 286 llamadas"
    ## [1] "Capturando los datos de id=77. Faltan 285 llamadas"
    ## [1] "Capturando los datos de id=63. Faltan 284 llamadas"
    ## [1] "Capturando los datos de id=117. Faltan 283 llamadas"
    ## [1] "Capturando los datos de id=80. Faltan 282 llamadas"
    ## [1] "Capturando los datos de id=290. Faltan 281 llamadas"
    ## [1] "Capturando los datos de id=336. Faltan 280 llamadas"
    ## [1] "Capturando los datos de id=301. Faltan 279 llamadas"
    ## [1] "Capturando los datos de id=318. Faltan 278 llamadas"
    ## [1] "Capturando los datos de id=137. Faltan 277 llamadas"
    ## [1] "Capturando los datos de id=329. Faltan 276 llamadas"
    ## [1] "Capturando los datos de id=309. Faltan 275 llamadas"
    ## [1] "Capturando los datos de id=130. Faltan 274 llamadas"
    ## [1] "Capturando los datos de id=167. Faltan 273 llamadas"
    ## [1] "Capturando los datos de id=254. Faltan 272 llamadas"
    ## [1] "Capturando los datos de id=351. Faltan 271 llamadas"
    ## [1] "Capturando los datos de id=212. Faltan 270 llamadas"
    ## [1] "Capturando los datos de id=160. Faltan 269 llamadas"
    ## [1] "Capturando los datos de id=135. Faltan 268 llamadas"
    ## [1] "Capturando los datos de id=24. Faltan 267 llamadas"
    ## [1] "Capturando los datos de id=353. Faltan 266 llamadas"
    ## [1] "Capturando los datos de id=73. Faltan 265 llamadas"
    ## [1] "Capturando los datos de id=262. Faltan 264 llamadas"
    ## [1] "Capturando los datos de id=194. Faltan 263 llamadas"
    ## [1] "Capturando los datos de id=13. Faltan 262 llamadas"
    ## [1] "Capturando los datos de id=4. Faltan 261 llamadas"
    ## [1] "Capturando los datos de id=292. Faltan 260 llamadas"
    ## [1] "Capturando los datos de id=277. Faltan 259 llamadas"
    ## [1] "Capturando los datos de id=325. Faltan 258 llamadas"
    ## [1] "Capturando los datos de id=121. Faltan 257 llamadas"
    ## [1] "Capturando los datos de id=59. Faltan 256 llamadas"
    ## [1] "Capturando los datos de id=291. Faltan 255 llamadas"
    ## [1] "Capturando los datos de id=38. Faltan 254 llamadas"
    ## [1] "Capturando los datos de id=74. Faltan 253 llamadas"
    ## [1] "Capturando los datos de id=20. Faltan 252 llamadas"
    ## [1] "Capturando los datos de id=142. Faltan 251 llamadas"
    ## [1] "Capturando los datos de id=133. Faltan 250 llamadas"
    ## [1] "Capturando los datos de id=248. Faltan 249 llamadas"
    ## [1] "Capturando los datos de id=273. Faltan 248 llamadas"
    ## [1] "Capturando los datos de id=268. Faltan 247 llamadas"
    ## [1] "Capturando los datos de id=227. Faltan 246 llamadas"
    ## [1] "Capturando los datos de id=146. Faltan 245 llamadas"
    ## [1] "Capturando los datos de id=147. Faltan 244 llamadas"
    ## [1] "Capturando los datos de id=204. Faltan 243 llamadas"
    ## [1] "Capturando los datos de id=331. Faltan 242 llamadas"
    ## [1] "Capturando los datos de id=185. Faltan 241 llamadas"
    ## [1] "Capturando los datos de id=71. Faltan 240 llamadas"
    ## [1] "Capturando los datos de id=15. Faltan 239 llamadas"
    ## [1] "Capturando los datos de id=235. Faltan 238 llamadas"
    ## [1] "Capturando los datos de id=101. Faltan 237 llamadas"
    ## [1] "Capturando los datos de id=275. Faltan 236 llamadas"
    ## [1] "Capturando los datos de id=249. Faltan 235 llamadas"
    ## [1] "Capturando los datos de id=343. Faltan 234 llamadas"
    ## [1] "Capturando los datos de id=274. Faltan 233 llamadas"
    ## [1] "Capturando los datos de id=244. Faltan 232 llamadas"
    ## [1] "Capturando los datos de id=169. Faltan 231 llamadas"
    ## [1] "Capturando los datos de id=87. Faltan 230 llamadas"
    ## [1] "Capturando los datos de id=344. Faltan 229 llamadas"
    ## [1] "Capturando los datos de id=199. Faltan 228 llamadas"
    ## [1] "Capturando los datos de id=288. Faltan 227 llamadas"
    ## [1] "Capturando los datos de id=69. Faltan 226 llamadas"
    ## [1] "Capturando los datos de id=357. Faltan 225 llamadas"
    ## [1] "Capturando los datos de id=195. Faltan 224 llamadas"
    ## [1] "Capturando los datos de id=159. Faltan 223 llamadas"
    ## [1] "Capturando los datos de id=61. Faltan 222 llamadas"
    ## [1] "Capturando los datos de id=326. Faltan 221 llamadas"
    ## [1] "Capturando los datos de id=289. Faltan 220 llamadas"
    ## [1] "Capturando los datos de id=114. Faltan 219 llamadas"
    ## [1] "Capturando los datos de id=281. Faltan 218 llamadas"
    ## [1] "Capturando los datos de id=324. Faltan 217 llamadas"
    ## [1] "Capturando los datos de id=66. Faltan 216 llamadas"
    ## [1] "Capturando los datos de id=89. Faltan 215 llamadas"
    ## [1] "Capturando los datos de id=99. Faltan 214 llamadas"
    ## [1] "Capturando los datos de id=265. Faltan 213 llamadas"
    ## [1] "Capturando los datos de id=241. Faltan 212 llamadas"
    ## [1] "Capturando los datos de id=316. Faltan 211 llamadas"
    ## [1] "Capturando los datos de id=180. Faltan 210 llamadas"
    ## [1] "Capturando los datos de id=165. Faltan 209 llamadas"
    ## [1] "Capturando los datos de id=21. Faltan 208 llamadas"
    ## [1] "Capturando los datos de id=240. Faltan 207 llamadas"
    ## [1] "Capturando los datos de id=339. Faltan 206 llamadas"
    ## [1] "Capturando los datos de id=116. Faltan 205 llamadas"
    ## [1] "Capturando los datos de id=143. Faltan 204 llamadas"
    ## [1] "Capturando los datos de id=346. Faltan 203 llamadas"
    ## [1] "Capturando los datos de id=192. Faltan 202 llamadas"
    ## [1] "Capturando los datos de id=327. Faltan 201 llamadas"
    ## [1] "Capturando los datos de id=111. Faltan 200 llamadas"
    ## [1] "Capturando los datos de id=208. Faltan 199 llamadas"
    ## [1] "Capturando los datos de id=239. Faltan 198 llamadas"
    ## [1] "Capturando los datos de id=170. Faltan 197 llamadas"
    ## [1] "Capturando los datos de id=86. Faltan 196 llamadas"
    ## [1] "Capturando los datos de id=166. Faltan 195 llamadas"
    ## [1] "Capturando los datos de id=157. Faltan 194 llamadas"
    ## [1] "Capturando los datos de id=315. Faltan 193 llamadas"
    ## [1] "Capturando los datos de id=264. Faltan 192 llamadas"
    ## [1] "Capturando los datos de id=175. Faltan 191 llamadas"
    ## [1] "Capturando los datos de id=96. Faltan 190 llamadas"
    ## [1] "Capturando los datos de id=50. Faltan 189 llamadas"
    ## [1] "Capturando los datos de id=136. Faltan 188 llamadas"
    ## [1] "Capturando los datos de id=53. Faltan 187 llamadas"
    ## [1] "Capturando los datos de id=85. Faltan 186 llamadas"
    ## [1] "Capturando los datos de id=259. Faltan 185 llamadas"
    ## [1] "Capturando los datos de id=305. Faltan 184 llamadas"
    ## [1] "Capturando los datos de id=184. Faltan 183 llamadas"
    ## [1] "Capturando los datos de id=39. Faltan 182 llamadas"
    ## [1] "Capturando los datos de id=172. Faltan 181 llamadas"
    ## [1] "Capturando los datos de id=210. Faltan 180 llamadas"
    ## [1] "Capturando los datos de id=144. Faltan 179 llamadas"
    ## [1] "Capturando los datos de id=9. Faltan 178 llamadas"
    ## [1] "Capturando los datos de id=161. Faltan 177 llamadas"
    ## [1] "Capturando los datos de id=330. Faltan 176 llamadas"
    ## [1] "Capturando los datos de id=333. Faltan 175 llamadas"
    ## [1] "Capturando los datos de id=354. Faltan 174 llamadas"
    ## [1] "Capturando los datos de id=124. Faltan 173 llamadas"
    ## [1] "Capturando los datos de id=260. Faltan 172 llamadas"
    ## [1] "Capturando los datos de id=36. Faltan 171 llamadas"
    ## [1] "Capturando los datos de id=35. Faltan 170 llamadas"
    ## [1] "Capturando los datos de id=284. Faltan 169 llamadas"
    ## [1] "Capturando los datos de id=232. Faltan 168 llamadas"
    ## [1] "Capturando los datos de id=110. Faltan 167 llamadas"
    ## [1] "Capturando los datos de id=123. Faltan 166 llamadas"
    ## [1] "Capturando los datos de id=234. Faltan 165 llamadas"
    ## [1] "Capturando los datos de id=196. Faltan 164 llamadas"
    ## [1] "Capturando los datos de id=30. Faltan 163 llamadas"
    ## [1] "Capturando los datos de id=295. Faltan 162 llamadas"
    ## [1] "Capturando los datos de id=178. Faltan 161 llamadas"
    ## [1] "Capturando los datos de id=119. Faltan 160 llamadas"
    ## [1] "Capturando los datos de id=60. Faltan 159 llamadas"
    ## [1] "Capturando los datos de id=82. Faltan 158 llamadas"
    ## [1] "Capturando los datos de id=112. Faltan 157 llamadas"
    ## [1] "Capturando los datos de id=218. Faltan 156 llamadas"
    ## [1] "Capturando los datos de id=338. Faltan 155 llamadas"
    ## [1] "Capturando los datos de id=46. Faltan 154 llamadas"
    ## [1] "Capturando los datos de id=140. Faltan 153 llamadas"
    ## [1] "Capturando los datos de id=319. Faltan 152 llamadas"
    ## [1] "Capturando los datos de id=355. Faltan 151 llamadas"
    ## [1] "Capturando los datos de id=270. Faltan 150 llamadas"
    ## [1] "Capturando los datos de id=29. Faltan 149 llamadas"
    ## [1] "Capturando los datos de id=8. Faltan 148 llamadas"
    ## [1] "Capturando los datos de id=115. Faltan 147 llamadas"
    ## [1] "Capturando los datos de id=151. Faltan 146 llamadas"
    ## [1] "Capturando los datos de id=186. Faltan 145 llamadas"
    ## [1] "Capturando los datos de id=164. Faltan 144 llamadas"
    ## [1] "Capturando los datos de id=297. Faltan 143 llamadas"
    ## [1] "Capturando los datos de id=282. Faltan 142 llamadas"
    ## [1] "Capturando los datos de id=32. Faltan 141 llamadas"
    ## [1] "Capturando los datos de id=104. Faltan 140 llamadas"
    ## [1] "Capturando los datos de id=44. Faltan 139 llamadas"
    ## [1] "Capturando los datos de id=2. Faltan 138 llamadas"
    ## [1] "Capturando los datos de id=51. Faltan 137 llamadas"
    ## [1] "Capturando los datos de id=342. Faltan 136 llamadas"
    ## [1] "Capturando los datos de id=209. Faltan 135 llamadas"
    ## [1] "Capturando los datos de id=188. Faltan 134 llamadas"
    ## [1] "Capturando los datos de id=257. Faltan 133 llamadas"
    ## [1] "Capturando los datos de id=162. Faltan 132 llamadas"
    ## [1] "Capturando los datos de id=253. Faltan 131 llamadas"
    ## [1] "Capturando los datos de id=58. Faltan 130 llamadas"
    ## [1] "Capturando los datos de id=356. Faltan 129 llamadas"
    ## [1] "Capturando los datos de id=293. Faltan 128 llamadas"
    ## [1] "Capturando los datos de id=139. Faltan 127 llamadas"
    ## [1] "Capturando los datos de id=5. Faltan 126 llamadas"
    ## [1] "Capturando los datos de id=109. Faltan 125 llamadas"
    ## [1] "Capturando los datos de id=314. Faltan 124 llamadas"
    ## [1] "Capturando los datos de id=182. Faltan 123 llamadas"
    ## [1] "Capturando los datos de id=154. Faltan 122 llamadas"
    ## [1] "Capturando los datos de id=41. Faltan 121 llamadas"
    ## [1] "Capturando los datos de id=200. Faltan 120 llamadas"
    ## [1] "Capturando los datos de id=83. Faltan 119 llamadas"
    ## [1] "Capturando los datos de id=312. Faltan 118 llamadas"
    ## [1] "Capturando los datos de id=300. Faltan 117 llamadas"
    ## [1] "Capturando los datos de id=214. Faltan 116 llamadas"
    ## [1] "Capturando los datos de id=42. Faltan 115 llamadas"
    ## [1] "Capturando los datos de id=12. Faltan 114 llamadas"
    ## [1] "Capturando los datos de id=365. Faltan 113 llamadas"
    ## [1] "Capturando los datos de id=267. Faltan 112 llamadas"
    ## [1] "Capturando los datos de id=155. Faltan 111 llamadas"
    ## [1] "Capturando los datos de id=287. Faltan 110 llamadas"
    ## [1] "Capturando los datos de id=360. Faltan 109 llamadas"
    ## [1] "Capturando los datos de id=25. Faltan 108 llamadas"
    ## [1] "Capturando los datos de id=125. Faltan 107 llamadas"
    ## [1] "Capturando los datos de id=191. Faltan 106 llamadas"
    ## [1] "Capturando los datos de id=217. Faltan 105 llamadas"
    ## [1] "Capturando los datos de id=152. Faltan 104 llamadas"
    ## [1] "Capturando los datos de id=332. Faltan 103 llamadas"
    ## [1] "Capturando los datos de id=94. Faltan 102 llamadas"
    ## [1] "Capturando los datos de id=321. Faltan 101 llamadas"
    ## [1] "Capturando los datos de id=228. Faltan 100 llamadas"
    ## [1] "Capturando los datos de id=122. Faltan 99 llamadas"
    ## [1] "Capturando los datos de id=173. Faltan 98 llamadas"
    ## [1] "Capturando los datos de id=10. Faltan 97 llamadas"
    ## [1] "Capturando los datos de id=221. Faltan 96 llamadas"
    ## [1] "Capturando los datos de id=303. Faltan 95 llamadas"
    ## [1] "Capturando los datos de id=252. Faltan 94 llamadas"
    ## [1] "Capturando los datos de id=62. Faltan 93 llamadas"
    ## [1] "Capturando los datos de id=177. Faltan 92 llamadas"
    ## [1] "Capturando los datos de id=3. Faltan 91 llamadas"
    ## [1] "Capturando los datos de id=78. Faltan 90 llamadas"
    ## [1] "Capturando los datos de id=352. Faltan 89 llamadas"
    ## [1] "Capturando los datos de id=150. Faltan 88 llamadas"
    ## [1] "Capturando los datos de id=304. Faltan 87 llamadas"
    ## [1] "Capturando los datos de id=118. Faltan 86 llamadas"
    ## [1] "Capturando los datos de id=72. Faltan 85 llamadas"
    ## [1] "Capturando los datos de id=348. Faltan 84 llamadas"
    ## [1] "Capturando los datos de id=148. Faltan 83 llamadas"
    ## [1] "Capturando los datos de id=302. Faltan 82 llamadas"
    ## [1] "Capturando los datos de id=350. Faltan 81 llamadas"
    ## [1] "Capturando los datos de id=43. Faltan 80 llamadas"
    ## [1] "Capturando los datos de id=105. Faltan 79 llamadas"
    ## [1] "Capturando los datos de id=283. Faltan 78 llamadas"
    ## [1] "Capturando los datos de id=145. Faltan 77 llamadas"
    ## [1] "Capturando los datos de id=179. Faltan 76 llamadas"
    ## [1] "Capturando los datos de id=70. Faltan 75 llamadas"
    ## [1] "Capturando los datos de id=322. Faltan 74 llamadas"
    ## [1] "Capturando los datos de id=34. Faltan 73 llamadas"
    ## [1] "Capturando los datos de id=229. Faltan 72 llamadas"
    ## [1] "Capturando los datos de id=245. Faltan 71 llamadas"
    ## [1] "Capturando los datos de id=98. Faltan 70 llamadas"
    ## [1] "Capturando los datos de id=95. Faltan 69 llamadas"
    ## [1] "Capturando los datos de id=65. Faltan 68 llamadas"
    ## [1] "Capturando los datos de id=106. Faltan 67 llamadas"
    ## [1] "Capturando los datos de id=246. Faltan 66 llamadas"
    ## [1] "Capturando los datos de id=306. Faltan 65 llamadas"
    ## [1] "Capturando los datos de id=340. Faltan 64 llamadas"
    ## [1] "Capturando los datos de id=345. Faltan 63 llamadas"
    ## [1] "Capturando los datos de id=153. Faltan 62 llamadas"
    ## [1] "Capturando los datos de id=328. Faltan 61 llamadas"
    ## [1] "Capturando los datos de id=307. Faltan 60 llamadas"
    ## [1] "Capturando los datos de id=129. Faltan 59 llamadas"
    ## [1] "Capturando los datos de id=97. Faltan 58 llamadas"
    ## [1] "Capturando los datos de id=280. Faltan 57 llamadas"
    ## [1] "Capturando los datos de id=76. Faltan 56 llamadas"
    ## [1] "Capturando los datos de id=56. Faltan 55 llamadas"
    ## [1] "Capturando los datos de id=183. Faltan 54 llamadas"
    ## [1] "Capturando los datos de id=363. Faltan 53 llamadas"
    ## [1] "Capturando los datos de id=335. Faltan 52 llamadas"
    ## [1] "Capturando los datos de id=261. Faltan 51 llamadas"
    ## [1] "Capturando los datos de id=285. Faltan 50 llamadas"
    ## [1] "Capturando los datos de id=364. Faltan 49 llamadas"
    ## [1] "Capturando los datos de id=1. Faltan 48 llamadas"
    ## [1] "Capturando los datos de id=243. Faltan 47 llamadas"
    ## [1] "Capturando los datos de id=27. Faltan 46 llamadas"
    ## [1] "Capturando los datos de id=16. Faltan 45 llamadas"
    ## [1] "Capturando los datos de id=337. Faltan 44 llamadas"
    ## [1] "Capturando los datos de id=197. Faltan 43 llamadas"
    ## [1] "Capturando los datos de id=272. Faltan 42 llamadas"
    ## [1] "Capturando los datos de id=163. Faltan 41 llamadas"
    ## [1] "Capturando los datos de id=92. Faltan 40 llamadas"
    ## [1] "Capturando los datos de id=90. Faltan 39 llamadas"
    ## [1] "Capturando los datos de id=323. Faltan 38 llamadas"
    ## [1] "Capturando los datos de id=138. Faltan 37 llamadas"
    ## [1] "Capturando los datos de id=168. Faltan 36 llamadas"
    ## [1] "Capturando los datos de id=193. Faltan 35 llamadas"
    ## [1] "Capturando los datos de id=269. Faltan 34 llamadas"
    ## [1] "Capturando los datos de id=107. Faltan 33 llamadas"
    ## [1] "Capturando los datos de id=88. Faltan 32 llamadas"
    ## [1] "Capturando los datos de id=206. Faltan 31 llamadas"
    ## [1] "Capturando los datos de id=222. Faltan 30 llamadas"
    ## [1] "Capturando los datos de id=18. Faltan 29 llamadas"
    ## [1] "Capturando los datos de id=313. Faltan 28 llamadas"
    ## [1] "Capturando los datos de id=349. Faltan 27 llamadas"
    ## [1] "Capturando los datos de id=334. Faltan 26 llamadas"
    ## [1] "Capturando los datos de id=213. Faltan 25 llamadas"
    ## [1] "Capturando los datos de id=203. Faltan 24 llamadas"
    ## [1] "Capturando los datos de id=174. Faltan 23 llamadas"
    ## [1] "Capturando los datos de id=158. Faltan 22 llamadas"
    ## [1] "Capturando los datos de id=366. Faltan 21 llamadas"
    ## [1] "Capturando los datos de id=48. Faltan 20 llamadas"
    ## [1] "Capturando los datos de id=216. Faltan 19 llamadas"
    ## [1] "Capturando los datos de id=49. Faltan 18 llamadas"
    ## [1] "Capturando los datos de id=75. Faltan 17 llamadas"
    ## [1] "Capturando los datos de id=149. Faltan 16 llamadas"
    ## [1] "Capturando los datos de id=26. Faltan 15 llamadas"
    ## [1] "Capturando los datos de id=23. Faltan 14 llamadas"
    ## [1] "Capturando los datos de id=250. Faltan 13 llamadas"
    ## [1] "Capturando los datos de id=207. Faltan 12 llamadas"
    ## [1] "Capturando los datos de id=54. Faltan 11 llamadas"
    ## [1] "Capturando los datos de id=256. Faltan 10 llamadas"
    ## [1] "Capturando los datos de id=266. Faltan 9 llamadas"
    ## [1] "Capturando los datos de id=226. Faltan 8 llamadas"
    ## [1] "Capturando los datos de id=14. Faltan 7 llamadas"
    ## [1] "Capturando los datos de id=100. Faltan 6 llamadas"
    ## [1] "Capturando los datos de id=103. Faltan 5 llamadas"
    ## [1] "Capturando los datos de id=6. Faltan 4 llamadas"
    ## [1] "Capturando los datos de id=93. Faltan 3 llamadas"
    ## [1] "Capturando los datos de id=263. Faltan 2 llamadas"
    ## [1] "Capturando los datos de id=215. Faltan 1 llamadas"
    ## [1] "Capturando los datos de id=52. Faltan 0 llamadas"

En la lista `collab` tenemos mucha información, pero podemos hacer un
poco de limpieza para generar una base de datos. Para eso, crearemos una
pequeña función que, para cada diputado, tome los datos que queremos
mantener y los ponga en un `data.frame`.

    recuperar_atributos <- function(x) {
        out <- data.frame("id"=as.numeric(x$id),
                          "nombre"=x$normalized$url,
                          "grupo"=x$grupo,
                          "circunscripcion"=x$circunscripcion,
                          "sexo"=x$sexo,
                          "antiguedad"=length(x$legislaturas),
                          "edad"=difftime(Sys.time(),
                                          strptime(x$fecha_nac, "%d/%M/%Y"),
                                          unit="days"))
        return(out)
    }

Ahora podemos recorrer la lista de diputados y extraer los atributos que
nos interesan. En lugar de un *loop* usaremos una forma más idiomática
en `R` de atravesar una lista.

    atributos <- lapply(diputados, recuperar_atributos)
    atributos <- do.call(rbind, atributos)
    atributos$id <- as.character(atributos$id)
    head(atributos)

    ##    id                           nombre
    ## 1 171            jose-luis-abalos-meco
    ## 2 131                pedro-acedo-penco
    ## 3 233 joseba-andoni-agirretxea-urresti
    ## 4   7         ernesto-aguiar-rodriguez
    ## 5 320          ramon-aguirre-rodriguez
    ## 6 187         nayua-miriam-alba-goveli
    ##                                                 grupo   circunscripcion
    ## 1                                          Socialista Valencia/València
    ## 2                                             Popular           Badajoz
    ## 3                                               Vasco          Gipuzkoa
    ## 4                                             Popular      S/C Tenerife
    ## 5                                             Popular       Guadalajara
    ## 6 Confederal de Unidos Podemos-En Comú Podem-En Marea          Gipuzkoa
    ##   sexo antiguedad       edad
    ## 1    H          3 21191 days
    ## 2    H          0 22660 days
    ## 3    H          3 18637 days
    ## 4    H          1 20443 days
    ## 5    H          6 23388 days
    ## 6    M          1  9861 days

Ahora que tenemos información acerca de los diputados (los vértices de
nuestra red), queremos recuperar las aristas que los unen. En esta red,
dos vértices están unidos por una arista si los vértices han colaborado
en una pieza de legislación.

    recuperar_coautores <- function(x) {
        out <- unique(unlist(lapply(x, function(y) unlist(y$autores))))
        return(out)
    }

Podemos aplicar la misma estrategia para crear la lista de coautores
para cada diputado

    autores <- lapply(collab, recuperar_coautores)
    head(autores)

    ## $`171`
    ##  [1] 171  28  30 237 283 351 176 288 193 238 138 284 179  31 102  50 260
    ## [18]  29 252  88 261 277 297 192 273 183 155  80 216 107 321 276 263
    ## 
    ## $`131`
    ## [1] 131
    ## 
    ## $`233`
    ## [1] 233
    ## 
    ## $`7`
    ## [1]  7 51 52
    ## 
    ## $`320`
    ## [1]  26 320
    ## 
    ## $`187`
    ##  [1] 187 173 186 328 188 219 345 279 196 243 318 163

eliminando además a los diputados que no han participado en ninguna
autoría

    autores <- autores[!sapply(autores, function(x) length(x) == 0)] ## Eliminar 0 autorias
    head(autores)

    ## $`171`
    ##  [1] 171  28  30 237 283 351 176 288 193 238 138 284 179  31 102  50 260
    ## [18]  29 252  88 261 277 297 192 273 183 155  80 216 107 321 276 263
    ## 
    ## $`131`
    ## [1] 131
    ## 
    ## $`233`
    ## [1] 233
    ## 
    ## $`7`
    ## [1]  7 51 52
    ## 
    ## $`320`
    ## [1]  26 320
    ## 
    ## $`187`
    ##  [1] 187 173 186 328 188 219 345 279 196 243 318 163

aunque podríamos tratarlos como *islas* de nuestra red.

Lo que tenemos ahora es una lista en la que para cada diputado tenemos
un vector que contiene los diputados con los que ha colaborado. Lo que
haremos a continuación es colapsar eso para tener una matriz.

    coautores <- lapply(1:length(autores),
                        function(x) cbind(as.numeric(names(autores)[x]),
                                          autores[[x]]))
    edgelist <- do.call(rbind, coautores)
    head(edgelist)

    ##      [,1] [,2]
    ## [1,]  171  171
    ## [2,]  171   28
    ## [3,]  171   30
    ## [4,]  171  237
    ## [5,]  171  283
    ## [6,]  171  351

Un último paso de limpieza. En la matriz tal y como la tenemos ahora,
tenemos *bucles*, es decir, tenemos listado a cada diputado colaborando
consigo mismo. No es necesariamente un problema, pero no es lo que nos
interesa en este análisis, así que es mejor que quitemos este tipo de
observaciones

    loops <- edgelist[, 1] == edgelist[, 2]
    edgelist <- edgelist[!loops, ]
    head(edgelist)

    ##      [,1] [,2]
    ## [1,]  171   28
    ## [2,]  171   30
    ## [3,]  171  237
    ## [4,]  171  283
    ## [5,]  171  351
    ## [6,]  171  176

Finalmente, guardaremos los datos en un archivo `.RDS` asegurándonos de
que la lista de aristas que acabamos de crear contenga los *nombres*
(las id numéricas) de cada diputados.

    edgelist <- apply(edgelist, 2, as.character)
    saveRDS(edgelist, "dta/edgelist-diputados.RDS")
    saveRDS(atributos, "dta/atributos-diputados.RDS")

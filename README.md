import streamlit as st

st.set_page_config(page_title="TP Arithmétique & RSA", page_icon="🔐", layout="wide")

st.markdown("""
    <style>
    .big-font { font-size:20px !important; font-weight: bold; color: #4A90E2; }
    .stButton>button { width: 100%; border-radius: 10px; height: 3em; background-color: #4A90E2; color: white; }
    .stButton>button:hover { background-color: #357ABD; color: white; }
    </style>
    """, unsafe_allow_html=True)

st.title("🔐 TP : Arithmétique & Chiffrement RSA")
st.markdown('<p class="big-font">👨‍🎓 Réalisé par : [Votre Nom] et [Nom de votre binôme] - IDRS</p>', unsafe_allow_html=True)
st.markdown("---")

def pgcd(a, b):
    while b != 0:
        a, b = b, a % b
    return a

def bezout(a, b):
    if b == 0:
        return a, 1, 0
    d, u1, v1 = bezout(b, a % b)
    return d, v1, u1 - (a // b) * v1

def inverse_mod(a, n):
    d, u, v = bezout(a, n)
    if d != 1:
        return None
    return (u % n + n) % n

def est_premier(x):
    if x < 2: return False
    for i in range(2, int(x**0.5) + 1):
        if x % i == 0: return False
    return True

st.header("🔒 Partie 1 : Chiffrement RSA (IDRS)")
st.info("Entrez les paramètres pour générer les clés et crypter le message.")

col_p, col_q, col_e, col_m = st.columns(4)
with col_p: p = st.number_input("Nombre premier p", value=11, step=1)
with col_q: q = st.number_input("Nombre premier q", value=17, step=1)
with col_e: e = st.number_input("Clé publique e", value=7, step=1)
with col_m: m = st.number_input("Message m", value=42, step=1)

st.write("") 

if st.button("🚀 Lancer le chiffrement et le déchiffrement RSA"):
    if not est_premier(p) or not est_premier(q):
        st.error("❌ Erreur : p ou q n'est pas un nombre premier !")
    else:
        n = p * q
        phi = (p - 1) * (q - 1)
        d = inverse_mod(e, phi)
         if d is None:
            st.error(f"❌ Erreur : e ({e}) et φ ({phi}) ne sont pas premiers entre eux. Pas d'inverse modulaire.")
        else:
            c = pow(m, e, n)
            m2 = pow(c, d, n)
               st.success("✅ Opération réussie !")
            res1, res2, res3, res4 = st.columns(4)
            res1.metric("Module n", n)
            res2.metric("Indicatrice φ(n)", phi)
            res3.metric("Clé privée d", d)
            res4.metric("Message chiffré c", c)
            st.markdown(f"**Message déchiffré m' :** `{m2}`")
            if m == m2:
                st.success("🎯 Succès : m' = m (Le message a été correctement déchiffré)")
            else:
                st.error("❌ Échec du déchiffrement (m' ≠ m)")

st.markdown("---")
st.header("🧮 Partie 2 : Tronc Commun (Algorithmes)")


col1, col2, col3 = st.columns(3)

with col1:
    st.subheader("1. Algorithme d'Euclide")
    st.caption("Calcul du PGCD")
    a_pgcd = st.number_input("Entrez a :", value=252, key="a_pgcd")
    b_pgcd = st.number_input("Entrez b :", value=198, key="b_pgcd")
    if st.button("Calculer PGCD"):
        res = pgcd(a_pgcd, b_pgcd)
        st.success(f"PGCD({a_pgcd}, {b_pgcd}) = **{res}**")

with col2:
    st.subheader("2. Théorème de Bézout")
    st.caption("Trouver u et v tels que au + bv = d")
    a_bez = st.number_input("Entrez a :", value=252, key="a_bez")
    b_bez = st.number_input("Entrez b :", value=198, key="b_bez")
    if st.button("Calculer Bézout"):
        d, u, v = bezout(a_bez, b_bez)
        st.success(f"**{a_bez} × ({u}) + {b_bez} × ({v}) = {d}**")
        st.info(f"u = {u} , v = {v}")

with col3:
    st.subheader("3. Inverse Modulaire")
    st.caption("Trouver x tel que ax ≡ 1 (mod n)")
    a_inv = st.number_input("Entrez a :", value=7, key="a_inv")
    n_inv = st.number_input("Entrez n :", value=160, key="n_inv")
    if st.button("Calculer l'inverse"):
        res = inverse_mod(a_inv, n_inv)
        if res is not None:
            st.success(f"L'inverse de {a_inv} modulo {n_inv} est **{res}**")
            st.caption(f"Vérification : ({a_inv} × {res}) mod {n_inv} = 1")
        else:
            st.error(f"Pas d'inverse car PGCD({a_inv}, {n_inv}) ≠ 1")

st.markdown("---")
st.caption("TP - Arithmétique Appliquée | Année universitaire 2026-2027")

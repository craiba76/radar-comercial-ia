import streamlit as st
import pandas as pd
import snscrape.modules.twitter as sntwitter

st.set_page_config(page_title="Radar Comercial por IA", layout="wide")
st.title("📡 Radar Comercial por IA - MVP")

st.markdown("""
Este radar encontra **pessoas reais** com **intenção de compra ou necessidade** usando palavras-chave no Twitter.
Ideal para nichos como saúde, beleza, pet, casa, tecnologia, etc.
""")

keyword = st.text_input("🔍 Digite palavras-chave (ex: dor no joelho, shampoo antiqueda):", "dor no joelho")
limit = st.slider("Quantidade de resultados:", 10, 100, 30)

if st.button("Buscar Pessoas Reais"):
    with st.spinner("Buscando no Twitter..."):
        tweets = []
        for i, tweet in enumerate(sntwitter.TwitterSearchScraper(f'{keyword} lang:pt since:2024-01-01').get_items()):
            if i >= limit:
                break
            tweets.append({
                'Usuário': tweet.user.username,
                'Nome': tweet.user.displayname,
                'Tweet': tweet.content,
                'Link': f"https://twitter.com/{tweet.user.username}/status/{tweet.id}",
                'Data': tweet.date.strftime('%Y-%m-%d %H:%M')
            })

        if tweets:
            df = pd.DataFrame(tweets)
            st.success(f"{len(df)} pessoas encontradas com intenção ou problema relacionado a: '{keyword}'")
            st.dataframe(df)
            st.download_button("📥 Baixar CSV", df.to_csv(index=False), file_name="leads.csv", mime="text/csv")
        else:
            st.warning("Nenhum resultado encontrado.")
# radar-comercial-ia

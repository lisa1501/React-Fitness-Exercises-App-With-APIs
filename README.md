# Welcome To Golds Gym
Golds Gym is a fitness application that allows users to watch workout exercises online. Users can search or select exercise categories and specific muscle groups.
After users select one of the exercises from the exercises list, users will navigate to a selected exercise detail page. 
This page provided users with exercise detail, related exercise Youtube videos, similar target muscle exercises, and similar equipment exercises.

<br>

[Click here to see it in action!](https://halisa-fitnees-app.netlify.app/#exercises)

<br>
# Technologies Used
 This application applied rapid API. The front end was created using Javascript and rendered using React. React was chosen for this project due to its lite footprint, quick response times to maximize user experience especially when there may be multiple query's happening per page, and strong ecosystem backing and support especially around libraries and other recourses to help maximize efficiency. 
<br>

![image](https://github.com/lisa1501/React-Fitness-Exercises-App-With-APIs/blob/main/src/images/herobanner.png)

# Components

## Home
On the main page, the application renders a search bar, horizontal scrollbar, and exercises list page which is paginated.
<br>

![image](https://github.com/lisa1501/React-Fitness-Exercises-App-With-APIs/blob/main/src/images/selectfromscrollbaritems.png)
###  - Search exercise
users can search exercises by inputting a search bar or selecting a category from the scroll bar.
<br>

```javascript
 //---------------------------------search exercise--------------------------------
 const SearchExercises = ({ setExercises, bodyPart, setBodyPart }) => {
    const [search, setSearch] = useState('')
    const [bodyParts, setBodyParts] = useState([])

    useEffect(() => {
        const fetchExercisesData = async () => {
            const bodyPartsData = await fetchData('https://exercisedb.p.rapidapi.com/exercises/bodyPartList',
            exerciseOptions);

            setBodyParts(['all', ...bodyPartsData]);
        }

        fetchExercisesData();
    },[])

    const handleSearch = async () => {
        if(search){
            const exercisesData = await fetchData(
                'https://exercisedb.p.rapidapi.com/exercises',exerciseOptions
            );
            const searchedExercises = exercisesData.filter(
                (exercise) => exercise.name.toLowerCase().includes(search)
                || exercise.target.toLowerCase().includes(search)
                || exercise.equipment.toLowerCase().includes(search)
                || exercise.bodyPart.toLowerCase().includes(search)
            );
            setSearch('');
            setExercises(searchedExercises)
;        }
    }
 }
//---------------------------------exercises--------------------------------
const Exercises = ({ setExercises, bodyPart, exercises}) => {
    const [currentPage, setCurrentPage] = useState(1);
    const exercisesPerPage = 9;

    useEffect(() => {
        const fetchExercisesData = async () => {
            let exercisesData = [];

            if(bodyPart === 'all') {
                exercisesData = await fetchData(
                    'https://exercisedb.p.rapidapi.com/exercises',exerciseOptions)
            } else {
                exercisesData = await fetchData(`https://exercisedb.p.rapidapi.com/exercises/bodyPart/${bodyPart}`,
                exerciseOptions);
            }
            setExercises(exercisesData);
        }
        fetchExercisesData();
    },[bodyPart]);

    //Pagination
    const indexOfLastExercise = currentPage * exercisesPerPage;
    const indexOfFirstExerciese = indexOfLastExercise - exercisesPerPage;
    const currentExercises = exercises.slice(indexOfFirstExerciese,indexOfLastExercise)

    const paginate = (event, value) =>{
        setCurrentPage(value);
        window.scrollTo({ top: 1800, behavior:'smooth'})
    }

    if (!currentExercises.length) return <Loader />;
}
 
```
<br>

## Exercise 
This page will provide users with selected exercise detail, related Youtube videos, similar exercises and equipment.
<br>

###  - exercise detail
![image](https://github.com/lisa1501/React-Fitness-Exercises-App-With-APIs/blob/main/src/images/selectedexercisedetail.png)
<br>

```javascript
 //---------------------------------exercise detail--------------------------------
 const Detail = ({ exerciseDetail }) => {
    const { bodyPart, gifUrl, name, target, equipment } = exerciseDetail;
    
    const extraDetail = [
        {
            icon: BodyPartImage,
            name: bodyPart,
        },
        {
            icon: TargetImage,
            name: target,
        },
        {
            icon: EquipmentImage,
            name: equipment,
        },
    ];

    return (
        <Stack gap="60px" sx={{flexDirection: { lg:'row'}, p: '20px', alignItems: 'ceter'}}>
        <img src={gifUrl} alt={name} loading="lazy" 
            className="detail-image"/>
        <Stack sx={{ gap:{ lg:'35px', xs:'20px' }}}>
            <Typography variant="h3">
                {name}
            </Typography>
            <Typography sx={{ fontSize: { lg: '24px', xs: '18px' } }} color="#4F4C4C">
                Exercises keep you strong.{' '}
                <span style={{ textTransform: 'capitalize' }}>{name}</span> bup is one
                of the best <br /> exercises to target your {target}. It will help you improve your{' '}
                <br /> mood and gain energy.
            </Typography>
            {extraDetail?.map((item) => (
                <Stack key={item.name} direction="row" gap="24px" alignItems="center">
                    <Button sx={{ background: '#FFF2DB', borderRadius: '50%', width: '100px', height: '100px' }}>
                        <img src={item.icon} alt={bodyPart} style={{ width: '50px', height: '50px' }} />
                    </Button>
                    <Typography textTransform="capitalize" sx={{ fontSize: { lg: '30px', xs: '20px' } }}>
                        {item.name}
                    </Typography>
                </Stack>
            ))}
        </Stack>
    </Stack>
    )
}
```
<br>

###  - exercise videos
![image](https://github.com/lisa1501/React-Fitness-Exercises-App-With-APIs/blob/main/src/images/exercisevideo.png)
<br>

```javascript
    
    //---------------------------------exercise videos--------------------------------
    const ExerciseVideos = ({ exerciseVideos, name }) => {
        if(!exerciseVideos.length) return 'Loading...'

        return (
            <Box sx={{ marginTop: { lg: '203px', xs: '20px' } }} p="20px">
                <Typography sx={{ fontSize: { lg: '44px', xs: '25px' } }} fontWeight={700} color="#000" mb="33px">
                    Watch <span style={{ color: '#FF2625', textTransform: 'capitalize' }}>{name}</span> exercise videos
                </Typography>
                <Stack sx={{ flexDirection: { lg: 'row' }, gap: { lg: '110px', xs: '0px' } }} justifyContent="flex-start" flexWrap="wrap" alignItems="center">
                    {exerciseVideos?.slice(0, 3)?.map((item, index) => (
                    <a
                        key={index}
                        className="exercise-video"
                        href={`https://www.youtube.com/watch?v=${item.video.videoId}`}
                        target="_blank"
                        rel="noreferrer"
                    >
                        <img style={{ borderTopLeftRadius: '20px' }} src={item.video.thumbnails[0].url} alt={item.video.title} />
                        <Box>
                            <Typography sx={{ fontSize: { lg: '28px', xs: '18px' } }} fontWeight={600} color="#000">
                                {item.video.title}
                            </Typography>
                            <Typography fontSize="14px" color="#000">
                                {item.video.channelName}
                            </Typography>
                        </Box>
                    </a>
                ))}
                </Stack>
            </Box>
        )
    }
```
<br>

###  - similar exercise 
![image](https://github.com/lisa1501/React-Fitness-Exercises-App-With-APIs/blob/main/src/images/similiarexercies.png))
<br>

```javascript
    
    //--------------------------------similar exercises and equipments--------------------------------
const SimilarExercises = ({ targetMuscleExercises, equipmentExercises }) => {
    return (
        <Box sx={{ mt: { lg: '100px', xs: '0px' } }}>
            <Typography sx={{ fontSize: { lg: '44px', xs: '25px' }, ml: '20px' }} fontWeight={700} color="#000" mb="33px">
                Similar <span style={{ color: '#FF2625', textTransform: 'capitalize' }}>Target Muscle</span> exercises
            </Typography>
            <Stack direction="row" sx={{ p: 2, position: 'relative' }}>
                {targetMuscleExercises.length !== 0 ? <HorizontalScrollbar data={targetMuscleExercises} /> : <Loader />}
            </Stack>
            <Typography sx={{ fontSize: { lg: '44px', xs: '25px' }, ml: '20px', mt: { lg: '100px', xs: '60px' } }} fontWeight={700} color="#000" mb="33px">
            Similar <span style={{ color: '#FF2625', textTransform: 'capitalize' }}>Equipment</span> exercises
            </Typography>
            <Stack direction="row" sx={{ p: 2, position: 'relative' }}>
                {equipmentExercises.length !== 0 ? <HorizontalScrollbar data={equipmentExercises} /> : <Loader />}
            </Stack>
        </Box>
    )
}
```
<br>

